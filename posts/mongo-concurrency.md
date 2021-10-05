##Concurrency with MongoDB Explained

Ref: https://learning.oreilly.com/library/view/secure-your-node-js/9781680501780/f_0037.xhtml#sec.conc-mongodb

Let’s move back to practical usage and look at MongoDB since it’s the most used NoSQL database for Node.js applications.

MongoDB doesn’t guarantee ACID compliance[40] in all cases. For example, MongoDB doesn’t have transactions and atomic functions that cover multiple documents. So if you have a database model where you need to avoid concurrency issues with multiple documents at the same time, you’re in trouble. For example, you might have an e-commerce application that needs to decrement the number of products in the warehouse inventory while at the same time withdrawing money from the user’s wallet.

ACID Compliance
When talking about concurrency and databases, the first thing that’s usually looked at is whether the database is ACID compliant. That doesn’t mean it must be able to withstand an acid attack; rather ACID stands for “Atomicity, Consistency, Isolation, Durability.”

What these mean:

Atomicity: operations must be atomic.
Consistency: every transaction must leave the database in a consistent (valid) state.
Isolation: transactions cannot interfere with each other.
Durability: effects of transactions must persist through crashes.
The reasoning is that concurrency issues are far easier to deal with in an ACID-compliant database.

Faced with this situation you are left the option to modify the database schema to collect information into single documents so that you can use atomic functions, or you can create a locking mechanism for a collection of documents. MongoDB has good information in its documentation about two-phase commit,[41] which is essentially a locking mechanism. Finally, you could also use another database, either in conjunction with MongoDB or completely migrate to that.

I think this is a good spot to point out that just because you’re using Node.js in your application, it doesn’t mean that a NoSQL database is the best solution for you—database selection should be determined by your application and information types and connections.

In this section, we’ll fix the example using locks and atomic functions. Locking is the more general solution because you can extend it over several operations and more complex applications; this specific example benefits more from atomic functions. So let’s look at the simpler atomic approach and then see how you can use locking.

To use atomic functions we need to modify our logic to deduct the amount first and then perform the operation. If anything goes wrong, we reimburse the amount:

`chp-6-concurrency/concurrency-wallet-mongo-delay-atomic.js`
```javascript
​ 	​// Our processing function​
​ 	​function​ processCall(cb) {
​ 	    ​// Add delay of 5 seconds here - imitating processing of the request​
​ 	    setTimeout(cb, 5000);
​ 	}
​ 	
​ 	app.post(​'/:name'​, ​function​(req, res, next){
​ 	    ​var​ amount = Math.abs(req.body.amount);
​ 	
	    ​// Search by name and amount greater than or equal to requested​
	    ​var​ search = {name: req.params.name, amount: {$gte: amount}};
	    ​// Increment by negative amount​
	    ​var​ update = {$inc: {amount: -amount}};
	    Wallet.findOneAndUpdate(search, update, ​function​ (err, wallet) {
​ 	        ​if​ (err) { ​// Something went wrong with the query​
​ 	            next(err);
​ 	            ​return​;
​ 	        }
​ 	        ​if​(!wallet) {
​ 	            res.send(400, ​'Insufficient funds or not found'​);
​ 	            ​return​;
​ 	        }
​ 	
​ 	        processCall(​function​ (err) {
	            ​if​(err) {
	                ​// Process failed so reimburse​
	                wallet.amount += amount;
	                wallet.save(​function​ (rErr, updatedW, rowsAffected) {
	                    ​if​(rErr || rowsAffected !== 1) {
	                        ​//TODO: This needs careful handling​
	                        console.error(​'Reimbursement failed'​);
	                    }
	                    res.send(500);
	                });
	                ​return​;
	            }
​ 	            res.redirect(​'/'​ + req.params.name);
​ 	        });
​ 	    });
​ 	});
```

And with these simple modifications our application is secure against concurrency attacks. All the calculations are atomic and the results will be correct.

Now let’s expand our database model to include a lock model so that you can see how to achieve the same thing with locking:

`chp-6-concurrency/concurrency-wallet-mongo-delay-lock.js`
```javascript
​ 	​var​ mongoose = require(​'mongoose'​);
​ 	​var​ args = require(​'minimist'​)(process.argv);
​ 	
​ 	​if​(!args.d) {
​ 	    console.log(​'This example requires the -d (mongoose db) command line variable'​);
​ 	    process.exit();
​ 	}
​ 	
​ 	​// Connect to mongoose db​
​ 	mongoose.connect(args.d);
​ 	mongoose.connection.on(​'error'​, ​function​ (err) {
​ 	    console.error(​'connection error:'​ + err);
​ 	    process.exit();
​ 	});
​ 	
​ 	​// Define wallet model​
​ 	​var​ walletSchema = ​new​ mongoose.Schema({
​ 	    name:  { type: String, required: ​true​, index: { unique: ​true​ } },
​ 	    amount: { type: Number, required: ​true​}
​ 	});
​ 	​var​ Wallet = mongoose.model(​'Wallet'​, walletSchema);
​ 	
​ 	​// Define wallet model​
​ 	​var​ lockSchema = ​new​ mongoose.Schema({
​ 	    name:  { type: String, required: ​true​, index: { unique: ​true​ } }
​ 	});
​ 	​var​ Lock = mongoose.model(​'Lock'​, lockSchema);
​ 	
​ 	Wallet.remove().exec(); ​// Delete all previous Wallets.​
```

At this point, we’ll create and apply the mechanism to lock the resources and release them afterward:

`chp-6-concurrency/concurrency-wallet-mongo-delay-lock.js`
```javascript
​ 	​// Our processing function​
​ 	​function​ processCall(cb) {
​ 	    ​// Add delay of 5 seconds here - imitating processing of the request​
​ 	    setTimeout(cb, 5000);
​ 	}
​ 	
	​function​ aquireLock(name, cb) {
	    ​// We will use the fact that name is unique and​
	    ​// so insert will fail if a lock exists​
	    Lock.create({ name: name }, ​function​ (err, lock) {
	        ​if​(err) {
	            cb(​new​ Error(​'Failed to aquire lock'​));
	            ​return​;
	        }
	        cb(​null​, lock);
	    });
	}
	
	​function​ releaseLock(name, cb) {
	    Lock.findOneAndRemove({name: name}, cb);
	}
​ 	
​ 	app.post(​'/:name'​, ​function​(req, res, next){
​ 	    ​var​ amount = Math.abs(req.body.amount);
​ 	
	    aquireLock(req.params.name, ​function​ (err) {
	        ​if​(err) {
	            res.send(409, ​'Already processing'​);
	            ​return​;
	        }
​ 	        Wallet.findOne({name: req.params.name}, ​function​ (err, wallet) {
​ 	            ​if​ (err) { ​// Something went wrong with the query​
​ 	                next(err);
​ 	                ​return​;
​ 	            }
​ 	            ​if​(!wallet) {
​ 	                res.send(404, ​'Not found'​);
​ 	                ​return​;
​ 	            }
​ 	            ​if​(wallet.amount < amount) {
​ 	                res.send(400, ​'Insufficient funds'​);
​ 	                ​return​;
​ 	            }
​ 	            processCall(​function​ () {
​ 	                wallet.amount -= amount;
​ 	                wallet.save(​function​ (rErr, updatedW, rowsAffected) {
​ 	                    ​if​(rErr || rowsAffected !== 1) {
​ 	                        res.send(500, ​'Withdrawal failed'​);
​ 	                        ​return​;
​ 	                    }
	                    releaseLock(req.params.name, ​function​ (err) {
	                        ​if​(err) {
	                            ​//FIXME: We should definitely handle the error here​
	                            console.error(err);
	                        }
	                        res.redirect(​'/'​ + req.params.name);
	                    });
​ 	                });
​ 	            });
​ 	        });
​ 	    });
​ 	});
```

We can acquire a lock atomically to the wallet and perform operations before releasing the lock. Because this example doesn’t take into account any potential errors, we can wind up with eternally locked resources. We’ll solve that problem by adding a timeout to the lock:

`chp-6-concurrency/concurrency-wallet-mongo-delay-lock-improved.js`
```javascript
​ 	​// Define wallet model​
​ 	​var​ lockSchema = ​new​ mongoose.Schema({
​ 	    name:  { type: String, required: ​true​, index: { unique: ​true​ } },
​ 	    timestamp: {type: Number}
​ 	});
​ 	​var​ Lock = mongoose.model(​'Lock'​, lockSchema);
```

Now when acquiring the lock, we’ll have to adjust for the timestamp to prevent eternal locks:

`chp-6-concurrency/concurrency-wallet-mongo-delay-lock-improved.js`
```javascript
​ 	​// Our processing function​
​ 	​function​ processCall(cb) {
​ 	    ​// Add delay of 5 seconds here - imitating processing of the request​
​ 	    setTimeout(cb, 5000);
​ 	}
​ 	
​ 	​function​ aquireLock(name, cb) {
	    ​var​ now = Date.now();
	    ​var​ expired = now - 60 * 1000;
	
	    ​// The basics of this command is that we either:​
	    ​// 1. Find an old lock and update it with a new timestamp​
	    ​// 2. Don't find one, in which case we try to insert​
	    ​//    This will either:​
	    ​//    2.1 fail, because of unique index - a lock exists​
	    ​//    2.2 succeeds - a new lock is created​
	    Lock.findOneAndUpdate({
	        name: name,
	        timestamp: {$lt: expired} ​//Include locks that are too old​
	    }, {
	        timestamp: now
	    }, {
	        ​'new'​: ​true​,   ​// return new doc if one is upserted​
	        upsert: ​true​ ​// insert the document if it does not exist​
	    }, ​function​ (err, lock) {
​ 	        ​if​(err) {
​ 	            cb(​new​ Error(​'Failed to aquire lock'​));
​ 	            ​return​;
​ 	        }
​ 	        cb(​null​, lock);
​ 	    });
​ 	}
​ 	
​ 	
​ 	​function​ releaseLock(name, cb) {
​ 	    Lock.findOneAndRemove({name: name}, cb);
​ 	}
```

Both of these solutions are valid. Atomic functions require you to change your operational logic only slightly, but you can see that locking mechanisms are more versatile.
