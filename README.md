# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
>In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

In the Observer pattern, declaring Subscriber as an interface type (or ```trait``` in Rust) ensures extensibility and adherence to SOLID principles. However, in BambangShop, only a single Model struct will suffice since there is only one type of Subscriber in view without the short-term expectation of introducing any others.

>id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

In this case, the use of ```DashMap``` is justified since it provides a direct, efficient map from product IDs to their subscribers. Unlike ```Vec``` where linear searches would be required for lookups, ```DashMap``` provides constant-time access that improves performance, especially as data grows. It also supports safe multithreaded access and is therefore more appropriate for multithreaded applications like BambangShop. Moreover, with a Singleton offering a centralised Subscriber map, there is no fragmentation of data. While a ```Vec``` would do the job for small-scale use cases, ```DashMap``` is the way to go for performance and scalability.

>When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

```DashMap``` and the Singleton pattern serve different but complementary roles in this case. The Singleton pattern, managed through ```lazy_static!```, ensures a single, centralized instance of the subscriber map, preventing unnecessary cloning and initialization. However, it does not inherently handle concurrent access issues. ```DashMap```, on the other hand, provides built-in thread safety, allowing multiple threads to safely access and modify the shared subscriber map without requiring manual synchronization. Since BambangShop operates in a multithreaded environment, both are necessary—Singleton ensures a single instance, while ```DashMap``` guarantees safe concurrent access.

#### Reflection Publisher-2

>In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Separating Service and Repository from the Model follows the Single Responsibility Principle (SRP) and Separation of Concerns, ensuring a modular and maintainable codebase. The Model represents database data, the Repository handles data access, and the Service processes and fetches data from the Repository. This separation reduces coupling, making the system more scalable, extensible, and easier to modify. It also improves testability by allowing independent testing of each component. Thus, this approach ensures cleaner architecture and smoother development.

>What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

If we place everything in the Model only, it would be full of business logic and data storage, and the code would be less maintainable. That kind of lack of separation of concerns would lead to tight coupling between models like Program, Subscriber, and Notification, with added complexity and reduced flexibility. That way, it would be much more difficult to change or scale the system.

>Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Postman is a powerful tool for testing web endpoints and ensuring responses match expectations. It allows easy customization of HTTP methods like CRUD without writing code, making debugging faster. Features like saving requests/responses, managing headers and cookies, and API documentation are especially useful for testing and collaboration. This makes Postman an helpful tool for both my group project and future software engineering work.

#### Reflection Publisher-3

>Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

In this tutorial, the push model is used. When changes occur, such as creation, deletion, or updates, the Notification service immediately sends updates to all subscribers without them needing to request it. This is implemented in the notify function, where the publisher actively pushes notifications to subscribers.

>What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

The pull model simplifies the observable and gives subscribers control over when and what data to retrieve. However, it can be inefficient since subscribers must constantly check for updates, leading to high CPU usage and potential delays in notifications. This could result in high latency compared to the push model, where updates are sent immediately. On the other hand, in the push model, a large number of subscribers could create a long queue, potentially delaying notifications due to computational limits.

>Explain what will happen to the program if we decide to not use multi-threading in the notification process.

This causes the process to halt while sending notifications, as it waits for each subscriber before proceeding. As the number of subscribers grows, this leads to higher latency and reduced efficiency. The sequential processing makes the system less scalable, slowing down notifications.