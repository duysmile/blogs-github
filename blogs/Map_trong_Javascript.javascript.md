### Dùng Map trong Javascript

Sau khi hiểu về hashmap thì bạn có thể dễ dàng nhận thấy là trong Javascript, chúng ta đang sử dụng plain object như là một hashmap, để lưu trữ dạng key pair, key-value. **Như vậy có thật sự tốt và tối ưu hay không ?** 

Thật ra trong JS, đã có một object được xây dựng sẵn (built-in object) để phục vụ cho việc sử dụng hashmap rồi, đó chính là `Map`.

Vậy việc sử dụng `Map` thay cho plain object thì có những ưu điểm gì, cùng xem tiếp nhé:

1. Nhiều kiểu dữ liệu cho key hơn
    - Nếu dùng object thì key chỉ có thể là `string` hoặc `symbol` nhưng với `Map` thì có thể là bất cứ gì, từ object, function hoặc là những kiểu nguyên thủy.

    ```jsx
    const map = new Map();
    const myFunction = () => console.log('I am a useful function.');
    const myNumber = 666;
    const myObject = {
      name: 'plainObjectValue',
      otherKey: 'otherValue'
    };
    map.set(myFunction, 'function as a key');
    map.set(myNumber, 'number as a key');
    map.set(myObject, 'object as a key');

    console.log(map.get(myFunction)); // function as a key
    console.log(map.get(myNumber)); // number as a key
    console.log(map.get(myObject)); // object as a key
    ```

2. Dễ dàng xác định kích thước hơn
    - Để biết được object có bao nhiêu key thì ta phải dùng `Object.keys(obj).length` (**độ phức tạp là O(n)**) trong khi để xác định kích thước của `Map` thì chỉ cần dùng thuộc tính `size` (**độ phức tạp là O(1)**).

    ```jsx
    const map = new Map();
    map.set('someKey1', 1);
    map.set('someKey2', 1);
    ...
    map.set('someKey100', 1);

    console.log(map.size) // 100, Runtime: O(1)

    const plainObjMap = {};
    plainObjMap['someKey1'] = 1;
    plainObjMap['someKey2'] = 1;
    ...
    plainObjMap['someKey100'] = 1;

    console.log(Object.keys(plainObjMap).length) // 100, Runtime: O(n)
    ```

3. Hiệu năng tốt hơn
    - `Map` được tối ưu cho việc thường xuyên thêm và xóa entries.
    - Hơn nữa, `Map` còn được tối ưu để đọc dữ liệu với độ phức tạp là một hằng số, còn đối với object thì phụ thuộc vào số lượng key **O(n).**
    - Và đối với `Map` thì key không cần được chuyển sang string nên giảm đi bớt thời gian lúc thêm entry.
4. Có thể lặp trực tiếp
    - Object thì cần phải được lấy ra tất cả key rồi lặp qua mảng key đó
    - Trong khi `Map` là iterable nên có thể lặp trực tiếp

    ```jsx
    const map = new Map();
    map.set('someKey1', 1);
    map.set('someKey2', 2);
    map.set('someKey3', 3);

    for (let [key, value] of map) {
      console.log(`${key} = ${value}`);
    }
    // someKey1 = 1
    // someKey2 = 2
    // someKey3 = 3

    const plainObjMap = {};
    plainObjMap['someKey1'] = 1;
    plainObjMap['someKey2'] = 2;
    plainObjMap['someKey3'] = 3;

    for (let key of Object.keys(plainObjMap)) {
      const value = plainObjMap[key];
      console.log(`${key} = ${value}`);
    }
    // someKey1 = 1
    // someKey2 = 2
    // someKey3 = 3
    ```

5. Thứ tự key
    - Trước ECMA 2015 thì những key được thêm vào object không được đảm bảo về mặt thứ tự, còn đối với `Map` thì thứ tự key chính là thứ tự lúc nó được thêm vào.
6. Không bị đè key
    - Nếu dùng plain object thì nó sẽ chứa một vài key có sẵn do prototype. Và điều này có thể gây ra xung đột giữa key bạn thêm vào và những key vô tình có sẵn này.
    - Đối với `Map` thì không có những key này khi khởi tạo.
    - Lưu ý: Từ ES6 thì bạn có thể tránh việc ghi đè key này bằng cách khởi tạo object bằng phương thức `Object.create(null)`.

    ```jsx
    const map = new Map();
    map.set('someKey1', 1);
    map.set('someKey2', 2);
    map.set('toString', 3); // No problem for Map

    const plainObjMap = {};
    plainObjMap['someKey1'] = 1;
    plainObjMap['someKey2'] = 2;
    plainObjMap['toString'] = 3; // Oops, native property
    ```

Tadaaa, vậy ở trên là tất cả những ưu điểm của `Map` so với `object` khi sử dụng nó như một Hashmap. Bạn có thể cân nhắc lại việc sử dụng đúng mục đích hơn trong JS nhé !
