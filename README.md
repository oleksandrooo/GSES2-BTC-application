# GSES2-BTC-application

## About this task

Hi. This was my internship assignment. I did it during July 23-30, 2022. I knew very little about Laravel and the API at the time, because I had done this [test task](https://github.com/oleksandrooo/testTask/) before. The task itself was described in Ukrainian, so below is the translation and the original of the task and my report

`` swagger: "2.0"
info:
  description: "
    
Тобі потрібно реалізувати сервіс з АРІ, який дозволить: 
- дізнатись поточний курс біткоіну (BTC) у гривні (UAH)
- підписати емейл на отримання інформації по зміні курсу
- запит, який відправить всім підписаним користувачам актуальний курс. 
Додаткові вимоги:
1. Сервіс має відповідати описаному ниже АРІ. <i>NB Закривати рішення аутентифікацією не потрібно</i>. 
2. Всі данні, для роботи додатку повинні зберігатися в файлах (підключати базу данних не потрібно). Тобто, потрібно реалізувати збереження та роботу з даними (наприклад, електронними адресами) через файлову систему.
3. В репозиторії повинен бути Dockerfile, який дозволяє запустити систему в Docker. З матеріалом по Docker вам необхідно ознайомитись самостійно.
4. Також ти можеш додавати коментарі чи опис логіки виконання роботи в README.md документі. Правильна логіка може стати перевагою при оцінюванні, якщо ти не повністю виконаєш завдання.
Очікувані мови виконання завдання: PHP, Go, JavaScript (Node.js). 
Виконувати завдання іншими мовами можна, проте, це не буде перевагою.
Виконане завдання необхідно завантажити на GitHub (публічний репозиторій) та сабмітнути виконане завдання в гугл-форму.
Ти можеш користуватися усією доступною інформацією, але виконуй завдання самостійно. 
Успіхів!
"
  version: "1.0.0"
  title: "GSES2 BTC application"
host: "gses2.app"
basePath: "/api"
tags:
- name: "rate"
  description: "Отримання поточного курсу BTC до UAH"
- name: "subscription"
  description: "Робота з підпискою"
schemes:
- "http"
paths:
  /rate:
    get:
      tags:
      - "rate"
      summary: "Отримати поточний курс BTC до UAH"
      description: "Запит має повертати поточний курс BTC до UAH використовуючи будь-який third party сервіс з публічним АРІ"
      operationId: "rate"
      produces:
      - "application/json"
      responses:
        "200":
          description: "Повертається актуальний курс BTC до UAH"
          schema:
            type: "number"
        "400":
          description: "Invalid status value"
  /subscribe:
    post:
      tags:
      - "subscription"
      summary: "Підписати емейл на отримання поточного курсу"
      description: "Запит має перевірити, чи немає данної електронної адреси в поточній базі даних (файловій) і, в разі її відсутності, записувати її. Пізніше, за допомогою іншого запиту ми будемо відправляти лист на ті електронні адреси, які будуть в цій базі. "
      operationId: "subscribe"
      consumes:
      - "application/x-www-form-urlencoded"
      produces:
      - "application/json"
      parameters:
      - name: "email"
        in: "formData"
        description: "Електронна адреса, яку потрібно підписати"
        required: true
        type: "string"
      responses:
        "200":
          description: "E-mail додано"
        "409":
          description: "Повертати, якщо e-mail вже є в базі даних (файловій)"
  /sendEmails:
    post:
      tags:
      - "subscription"
      summary: "Відправити e-mail з поточним курсом на всі підписані електронні пошти."
      description: "Запит має отримувати актуальний курс BTC до UAH за допомогою third-party сервісу та відправляти його на всі електронні адреси, які були підписані раніше.  "
      operationId: "sendEmails"
      produces:
      - "application/json"
      responses:
        "200":
          description: "E-mailʼи відправлено" ``




Вітаю! Ось моє виконання кейсового завдання в Software Engineering School. Нижче можна прочитати інструкцію та як я покроково виконував цей кейс


## Інструкція по встановленню

Я вважаю що ви її і так знаєте, але все ж вважаю за потрібним написати її
Додати відповідний домен в `hosts` файл:

```
127.0.0.1 http://gses2.app/
```

Клонувати репозиторій:

```
$ git clone https://github.com/oleksandroo/GSES2-BTC-application.git && cd GSES2-BTC-application
```

**Решта команд запускаються з кореня проекту.**

Скопіюйте `.env.example` до `.env` як у корені проекту, так і в `src/task`:

```
$ cp .env.example .env && cd src/task && cp .env.example .env && cd ../..
```

Встановіть залежності:

```
$ docker compose run --rm task composer install
```

Запустіть проект:

```
$ docker compose up -d
```

Після запуску проекту згенеруйте ключ Laravel:

```
$ docker compose exec task php artisan key:generate
```

## Виконання кейсу

Вітаю! Щоб було більш чітко зрозуміла логіка виконання завдання, я вирішив описати покроково що і як я робив, щоб виконати завдання. Сподіваюсь за це не отримаю менше балів

### 0. Підготовка до розробки
Розробку я проводив у WSL2 на базі Ubuntu 20.04 LTS (GNU/Linux 5.10.102.1-microsoft-standard-WSL2 x86_64), в якості IDE використовував PHPStorm і  Postman як API клієнт

Сам код я вирішив писати на РНР використовуючи фреймворк Laravel. Такий вибір обумовлений тим що РНР я доволі непогано знаю, а з Laravel хоч майже не працював і це був челендж для мене за досить короткий проміжок часу розібратись з ним, але знаю що реалізований функціонал для легшого і правильного написання АРІ

### 1. Підготовка оточення
Основні моменти:
	- В завданні не згадувалось, але зробив docker-compose.yml файл щоб у разі необхідності можна було додати новий контейнер (з БД, наприклад), та і не вважав правильним впихати все в Dockerfile
	- Не використовував Laravel Sail, бо Sail призначений для програм, які повністю розроблені в Laravel, на відміну від програм, де Laravel це лише один з компонентів. Тому вирішив підключити Laravel як компонент, щоб можна було підключити й інші компоненти
	- В Dockerfile створив користувача task щоб контейнери не використовували користувача root 
	
Але тут я стикнувся з проблемою що ніяк не міг правильно налаштувати nginx і не міг отримати доступу до сайту за посиланням gses2.app, лише через localhost, яку так і не зміг вирішити. Тому в мене Base URL виглядає як localhost/api/

### 2. Реалізація функціоналу
Перед тим як писати код, я уявив як має виглядати готовий код, в чому допоміг опис кейсового завдання.
У нас є дві сущності - rate (робота з курсом ВТС) і subscription (робота з підписками), які можна уявити як два різних контролери. В першому контролері є метод rate, а в другому два методи: subscribe і sendEmails. Ці ж методи використовують у своїй роботі різні сервіси - сервіс для роботи з HTTP запитами, сервіс для роботи з електронними адресами і сервіс для роботи з поштою
І вже відштовхуючись від цього почав реалізовувати відповідні контролери і сервіси
Тому перед реалізацією методів я створив відповідні контролери, прописав роути і перевірив чи все працює
![image](https://user-images.githubusercontent.com/109922489/182086718-d99868a5-649e-45fe-a251-7ccac4225f79.png)
#### 2.1. Реалізація методу rate
Для реалізації цього методу  мені спершу треба реалізувати сервіс який буде через публічний АРІ отримувати курс ВТС. В якості параметрів передається лінк за яким будем здійснювати запит і параметри
![image](https://user-images.githubusercontent.com/109922489/182086766-91e005ee-744a-4571-b4e8-40a5bf766655.png)
В контролері я передаю лінк (його зберіг у спеціально створеному файлі конфігурації task.php) і параметри, і натомість отримую відповідь і її передаю 
![image](https://user-images.githubusercontent.com/109922489/182086798-7b9a3bd4-f21d-4d75-b860-d6548bb9e3ab.png)
І в результаті отримую поточний курс
![image](https://user-images.githubusercontent.com/109922489/182086812-d86af1a5-793d-47c8-bd12-71b6cc866b4c.png)
#### 2.2. Реалізація методу subscribe
Для цього створюю EmailService, який буде працювати з емейлами, і в ньому реалізовую метод addNewEmail, який зберігає передану пошту за вказаним шляхом, і повертає true якщо вдалось додати, і false якщо емейл вже був доданий раніше
![image](https://user-images.githubusercontent.com/109922489/182086834-f6c4c11a-8f7b-46ec-b162-a39a54d38d39.png)
І переконуюсь що все працює як треба
Перший запит
![image](https://user-images.githubusercontent.com/109922489/182086850-e845c1d6-6403-4aa4-b251-7d4e2ea86bc4.png)
Повторний запит
![image](https://user-images.githubusercontent.com/109922489/182086865-dcd2be80-9436-4a47-984e-7b682e3bddfc.png)
#### 2.3. Реалізація методу sendEmails
Для цього я спочатку реалізував метод getAllEmails в EmailService, який витягує всі емейли, або повертає null, якщо файлу не існує. 
![image](https://user-images.githubusercontent.com/109922489/182086884-d9cd745e-53bf-4a70-a4c4-8b4e5decfcb6.png)
Опісля реалізував сервіс EmailService, в якому реалізував власне відправку повідомлень. Для відправлення повідомлень я використав сервіс mailtrap.io, конфігурацію якого прописав в .env файлі, і також створив розмітку і клас RateMail похідний від Mailable
![image](https://user-images.githubusercontent.com/109922489/182086948-c6bc0a67-ae96-4b39-9eec-31568395a61b.png)
І нарешті поєднав все у контролері
![image](https://user-images.githubusercontent.com/109922489/182086975-4682f79c-50f7-4aee-a98c-63b6c0b0353e.png)
Перевіряю
![image](https://user-images.githubusercontent.com/109922489/182086990-91aa3e8c-d323-4d70-9d3f-a3da0a936720.png)
![image](https://user-images.githubusercontent.com/109922489/182087005-9c0b029b-98b6-4f5f-ae34-3795eef45aee.png)

### Результати
Орієнтуючись  на кейс, мені вдалось майже повністю його виконати. І тепер хочу дати сам собі фідбек на кейс і написати що варто було б реалізувати:
Вдалось - дотриматись майже всі вимоги, не впихнув весь код куди не треба, а наскільки мені дозволяли мої знання дотримувався принципів MVC
Варто було б зробити краще:
- краще розібратись з nginx i docker щоб вирішити проблему з хостом
- краще вивчити Laravel щоб знати основні принципи і як правильно писати код
- покрити код тестами
