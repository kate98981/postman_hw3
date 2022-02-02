# postman_hw3
## /login
### 1. необходимо залогиниться
## /user_info
### 2. {{url}}/user_info
Тесты:
1) Статус код 200
```
pm.test("Status code is 200", function(){
    pm.response.to.have.status(200);
});
```
2) Проверка структуры json в ответе.
```
const schema = {
    "type": "object",
    "properties": {
        "start_qa_salary": {"type": "number"},
        "qa_salary_after_6_months": {"type": "number"},
        "qa_salary_after_12_months": {"type": "number"},
        "person":{
            "type": "object",
            "properties": {
                "u_name": {
                    "type": "array",
                    "items": [
                        {"type": "string"},
                        {"type": "integer"},
                        {"type": "integer"}
                    ]
                    },
                "u_age": {"type": "number"},
                "u_salary_1_5_year": {"type": "number"}
            },
            "additionalProperties": false
        }
    },
    "additionalProperties": false
}

pm.test("Schema is valid", function(){
    pm.response.to.have.jsonSchema(schema)
});
```
3) В ответе указаны коэффициенты умножения salary, напишите тесты по проверке правильности результата перемножения на коэффициент.
```
var res = pm.response.json();
var salary = pm.environment.get("salary")
pm.test("correct coefficient = 2", function(){
    pm.expect(Number(salary) * 2).to.eql(res.qa_salary_after_6_months)
});
pm.test("correct coefficient = 2.9", function(){
    pm.expect(Number(salary) * 2.9).to.eql(res.qa_salary_after_12_months)
});
pm.test("correct coefficient = 4", function(){
    pm.expect(Number(salary) * 4).to.eql(res.person.u_salary_1_5_year)
})
```
4) Достать значение из поля 'u_salary_1.5_year' и передать в поле salary запроса {{url}}/get_test_user
```
var newSalary = res.person.u_salary_1_5_year
pm.environment.set("newSalary", newSalary)
```
## /new_data
### 3. {{url}}/new_data
Тесты:
1) Статус код 200
```
pm.test("Status is 200 OK", function(){
    pm.response.to.have.status(200)
})
```
2) Проверка структуры json в ответе.
```
var schema = {
    "type": "object",
    "properties": {
    "age": {
      "type": "integer"
    },
    "name": {
      "type": "string"
    },
    "salary": {
      "type": "array",
      "items": [
        {
          "type": "integer"
        },
        {
          "type": "string"
        },
        {
          "type": "string"
        }  
      ]
    }
  },
  "required": ["age","name","salary"]
}
pm.test("Json is valid", function(){
    pm.response.to.have.jsonSchema(schema)
})
```
3) В ответе указаны коэффициенты умножения salary, напишите тесты по проверке правильности результата перемножения на коэффициент.
```
var salary = pm.environment.get("salary")
var res = pm.response.json();

pm.test("correct coefficient = 2", function(){
    pm.expect(Number(salary) * 2).to.eql(Number(res.salary[1]))
})
pm.test("correct coefficient = 3", function(){
    pm.expect(Number(salary) * 3).to.eql(Number(res.salary[2]))
})
```
4) проверить, что 2-й элемент массива salary больше 1-го и 0-го
```
pm.test("salary[2] > salary[0] and salary[2] > salary[1]", function(){
    pm.expect(Number(res.salary[2])).to.greaterThan(res.salary[0])
    pm.expect(Number(res.salary[2])).to.greaterThan(Number(res.salary[1]))
})
```
## /test_pet_info
### 4. {{url}}/test_pet_info
Тесты:
1) Статус код 200
```
pm.test("Status is 200 OK", function(){
    pm.response.to.have.status(200)
})
```
2) Проверка структуры json в ответе.
```
var schema = {
    "type": "object",
    "properties": {
        "age": {"type": "integer"},
        "daily_food": {"type": "number"},
        "daily_sleep": {"type": "number"},
        "name": {"type": "string"}
    },
    "required": ["age","daily_food","daily_sleep","name"]
}

pm.test("Json is valid", function(){
    pm.response.to.have.jsonSchema(schema)
})
```
3) В ответе указаны коэффициенты умножения weight, напишите тесты по проверке правильности результата перемножения на коэффициент.
```
var req = request.data
var res = pm.response.json()
pm.test("weight.coef = 0.012", function(){
    pm.expect(Number(req.weight) * 0.012).to.eql(res.daily_food)
})
pm.test("weight.coef = 2.5", function(){
    pm.expect(Number(req.weight) * 2.5).to.eql(res.daily_sleep)
})
```
## /get_test_user
### 5. {{url}}/get_test_user
Тесты:
1) Статус код 200
```
pm.test("Status is 200 OK", function(){
    pm.response.to.have.status(200)
})
```
2) Проверка структуры json в ответе.
```
var schema = {
    "type": "object",
    "properties": {
    "age": {"type": "string"},
    "family": {
      "type": "object",
      "properties": {
        "children": {
          "type": "array",
          "items": [
            {
              "type": "array",
              "items": [
                {"type": "string"},
                {"type": "integer"}
              ]
            },
            {
              "type": "array",
              "items": [
                  {"type": "string"},
                  {"type": "integer"}
                  ]
            }
          ]
        },
        "u_salary_1_5_year": {"type": "integer"}
      },
      "required": ["children","u_salary_1_5_year"]
    },
    "name": {"type": "string"},
    "salary": {"type": "integer"}
    },
    "required": ["age","family","name","salary"]
}

pm.test("Json is valid", function(){
    pm.response.to.have.jsonSchema(schema)
})
```
3) Проверить что занчение поля name = значению переменной name из окружения
```
var res = pm.response.json()
pm.test("Name = name from environment", function(){
    pm.expect(res.name).to.eql(pm.environment.get("name"))
})
```
4) Проверить что занчение поля age в ответе соответсвует отправленному в запросе значению поля age
```
var req = request.data;
pm.test("age from res = age from req", function(){
    pm.expect(Number(res.age)).to.eql(Number(req.age))
})
```
## /currency
### 6. {{url}}/currency
Тесты:
1) Можете взять любой объект из присланного списка, используйте js random.
```
var res = pm.response.json() //получила список валют
var arr_len = res.length

var min = Math.ceil(0);
var max = Math.floor(arr_len)
var ran = Math.floor(Math.random() * (max - min)) + min;
pm.environment.set("Cur_ID", res[ran].Cur_ID)
```
В объекте возьмите Cur_ID и передать через окружение в следующий запрос.
```
var cur_id = pm.environment.get("Cur_ID")
```

## /curr_byn
### 7. {{url}}/curr_byn
Тесты:
1) Статус код 200
```
pm.test("status is 200 OK", function(){
    pm.response.to.have.status == 200
});
```
2) Проверка структуры json в ответе.
```
var schema = {
    "type": "object",
    "properties": {
    "Cur_Abbreviation": {"type": "string"},
    "Cur_ID": {"type": "integer"},
    "Cur_Name": {"type": "string"},
    "Cur_OfficialRate": {"type": "number"},
    "Cur_Scale": {"type": "integer"},
    "Date": {"type": "string"}
    },
    "required": ["Cur_Abbreviation","Cur_ID","Cur_Name","Cur_OfficialRate","Cur_Scale","Date"]
}

pm.test("json is valid", function(){
    pm.response.to.have.jsonSchema(schema);
})
```
## ***
### {{url}}/currency
1) получить список валют
```
var res = pm.response.json()
```
2) итерировать список валют
3) в каждой итерации отправлять запрос на сервер для получения курса каждой валюты
4) если возвращается 500 код, переходим к следующей итреации
5) если получаем 200 код, проверяем response json на наличие поля "Cur_OfficialRate"
6) если поле есть, пишем в консоль инфу про валюту в виде response
{
    "Cur_Abbreviation": str
    "Cur_ID": int,
    "Cur_Name": str,
    "Cur_OfficialRate": float,
    "Cur_Scale": int,
    "Date": str
}
7) переходим к следующей итерации
```
var arr_len = res.length

var min = Math.ceil(0);
var max = Math.floor(arr_len)
var ran = Math.floor(Math.random() * (max - min)) + min;
pm.environment.set("Cur_ID", res[ran].Cur_ID)

var cur_id = 0;
var tok = pm.environment.get("token")  

for(let i = 0; i < arr_len; i++){
    cur_id = res[i].Cur_ID;
    const regRequest = {
        'method': 'POST',
        'url': 'http://162.55.220.72:5005/curr_byn',
        'body': {
            'mode': 'formdata',
            'formdata': [
                {'key': 'auth_token', 'value': tok},
                {'key': 'curr_code', 'value': cur_id}
            ]
        }
    };
    pm.sendRequest(regRequest, (error, response) => {
        if(response.code == 200){
            var resp = response.json();
            if(pm.expect(resp).have.to.property("Cur_OfficialRate")){
                console.log(resp)
            };
        }
    });
}
```
