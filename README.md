# WebAPI-VueJS-Task

1. Запускаем DepartmentAPI.
2. С помощью Postman отправляем несколько POST запросов на DepartmentAPI (https://localhost:port/api/departments) для создания департаментов. Например:
        {
          "Title":"IT"
        },
        {
          "Title":"Managers"
        }.
3. Запускаем UsersAPI.
4. В файле client/src/components/Empl.vue объявлены две переменные для хранения пути к API: UsersAPIPath и DepartmentAPIPath. Меняем номера портов.
5. Запускаем client.

В приложении реализованы все CRUD операции.

![app](https://i.imgur.com/cq7kKRk.png)
