# pizza-drone-miniproject

Welcome to the Drone-Based Pizza Delivery System! This project simulates a system where drones are utilized for pizza delivery. It is divided into two main components: the Control Center and the Drone Pizzeria.

## Control Center

The Control Center is the component of the system that manages the drones. It handles tasks such as adding a new drone, updating a drone, deleting a drone, dispatching a drone for delivery, and returning a drone to the fleet.

Main Features:

- Drone Management: Add, update, delete, dispatch for delivery, and return drones in the fleet.
- API Endpoints: RESTful API Endpoints for managing drones.

## Drone Pizzeria

The Drone Pizzeria is the component of the system that handles pizza orders. It is responsible for tasks such as receiving orders from customers, preparing pizzas, and passing on delivery tasks to the Control Center, which then assigns a drone for the delivery.

Main Features:

- Pizza Order Management: Place pizza orders with customer details and destination, view a list of pizza orders, and retrieve a specific pizza order by its id.
- API Endpoints: RESTful API Endpoints for managing pizza orders.

## Technologies

- Spring Boot
- Spring Data JPA
- MySQL
- RESTful APIs
- ActiveMQ

## Getting Started

1.  **Clone the Repository:**

    ```bash
    git clone https://gitlab.tamk.cloud/sw-architectures-and-design-2023-jingjing-yang/pizza-drone-miniproject.git
    ```

2.  **Build and Run:**
    You need to have a MySQL database set up with the appropriate schema. The `spring.jpa.hibernate.ddl-auto=update` property in the application properties will automatically create the necessary tables based on the entity classes.

    ````# 1. Install MySQL if you haven't already.
    (base) jingjingyang@jingjings-MacBook-Pro ~ % mysql -u root -p
    Enter password:
    Welcome to the MySQL monitor. Commands end with ; or \g.
    Your MySQL connection id is 8
    Server version: 8.0.35 MySQL Community Server - GPL

        Copyright (c) 2000, 2023, Oracle and/or its affiliates.

        Oracle is a registered trademark of Oracle Corporation and/or its
        affiliates. Other names may be trademarks of their respective
        owners.

        Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

        # 2. Create a database named "drone_management_db".
        mysql> CREATE DATABASE drone_management_db;
        Query OK, 1 row affected (0.01 sec)

        # 3. Create a MySQL user named "springuser" with password "password".
        mysql> CREATE USER 'springuser'@'localhost' IDENTIFIED BY 'password';
        Query OK, 0 rows affected (0.02 sec)

        # 4. Grant all privileges on "drone_management_db" to "springuser".
        mysql> GRANT ALL PRIVILEGES ON drone_management_db.* TO 'springuser'@'localhost';
        Query OK, 0 rows affected (0.00 sec)

        mysql> FLUSH PRIVILEGES;
        Query OK, 0 rows affected (0.00 sec)

        mysql> USE drone_management_db;
        Reading table information for completion of table and column names
        You can turn off this feature to get a quicker startup with -A

        Database changed
        ```
    ````

You also need to have an ActiveMQ broker running at `tcp://localhost:61616` as specified in the application properties.

        (base) jingjingyang@jingjings-MacBook-Pro ~ % which activemq
        /opt/homebrew/bin/activemq
        (base) jingjingyang@jingjings-MacBook-Pro ~ % cd /opt/homebrew/bin
        (base) jingjingyang@jingjings-MacBook-Pro bin % ./activemq start
        INFO: Loading '/opt/homebrew/Cellar/activemq/6.0.1/libexec//bin/setenv'
        INFO: Using java '/opt/homebrew/opt/openjdk/libexec/openjdk.jdk/Contents/Home/bin/java'
        INFO: Starting - inspect logfiles specified in logging.properties and log4j2.properties to get details
        INFO: pidfile created : '/opt/homebrew/Cellar/activemq/6.0.1/libexec//data/activemq.pid' (pid '12908')

        (base) jingjingyang@jingjings-MacBook-Pro bin % sudo lsof -i :61616
        Password:
        COMMAND   PID         USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
        java    12908 jingjingyang  141u  IPv6 0xfef9e61b97e69157      0t0  TCP localhost:61616->localhost:55166 (ESTABLISHED)
        java    12908 jingjingyang  146u  IPv6 0xfef9e61b97e6e957      0t0  TCP *:61616 (LISTEN)
        java    12908 jingjingyang  167u  IPv6 0xfef9e61b97e7b957      0t0  TCP localhost:61616->localhost:55178 (ESTABLISHED)
        java    20357 jingjingyang  100u  IPv6 0xfef9e61b97e84957      0t0  TCP localhost:55166->localhost:61616 (ESTABLISHED)
        java    20758 jingjingyang  118u  IPv6 0xfef9e61b904e0957      0t0  TCP localhost:55178->localhost:61616 (ESTABLISHED)

Once these are set up, you can generate Maven Wrapper files by running `mvn wrapper:wrapper` from the root directory of your project, then navigate to each submodule directory and run the applications using the Spring Boot Maven plugin with the command `mvn spring-boot:run`.

## Access the Application

- The Control Center API is available at [http://localhost:8020](http://localhost:8020/).
- The Drone Pizzeria API is available at [http://localhost:8070](http://localhost:8070/).
- The Control Center UI form is available at http://localhost:8020/api/fleet/drone-list.
- The Drone Pizzeria Order Form is available at http://localhost:8070/api/pizzaOrders/order-form.
- Control Center API Endpoints:
  - `GET /api/fleet/drones`: Returns a list of all drones in the fleet.
  - `GET /api/fleet/drones/{id}`: Returns the drone with the specified ID.
  - `POST /api/fleet/drones`: Adds a new drone to the fleet.
  - `PUT /api/fleet/drones/{id}`: Updates the details of the drone with the specified ID.
  - `DELETE /api/fleet/drones/{id}`: Deletes the drone with the specified ID.
  - `PUT /api/fleet/drones/{id}/return`: Instructs the drone with the specified ID to return to base.
- Drone Pizzeria API Endpoints:

  - `GET /api/pizzaOrders`: Retrieves a list of all pizza orders.
  - `GET /api/pizzaOrders/{orderId}`: Retrieves a specific pizza order by its `orderId`.
  - `POST /api/pizzaOrders`: Adds a new pizza order.
  - `POST /api/pizzaOrders/place-order`: Places a new pizza order using the web-based pizza order form.
  - `POST /api/pizzaOrders/update-order-status`: Update the status of an order.

## Example of Usage

1. **Scenario: Order with No Available Drones**

   ```bash
   (base) jingjingyang@jingjings-MacBook-Pro ~ % curl http://localhost:8020/api/fleet/drones
   []%
   ```

   Bob Johnson places an order for a Veggie pizza to be delivered to 789 Pine St. This is done via a POST request to the `/api/pizzaOrders` endpoint with the order details sent in JSON format.

   ```bash
   (base) jingjingyang@jingjings-MacBook-Pro ~ % curl -X POST -H "Content-Type: application/json" -d '{
     "customerName": "Bob Johnson",
     "pizzaType": "Veggie",
     "destination": "789 Pine St"
   }' http://localhost:8070/api/pizzaOrders
   {"orderId":3,"customerName":"Bob Johnson","pizzaType":"Veggie","pizzaStore":null,"destination":"789 Pine St","orderStatus":null}%
   ```

   When the order is placed, the system receives a POST request with the order details in JSON format. The system creates a new PizzaOrder object with the provided details and saves it to the database. The order details are then converted to a JSON string and sent as a message to the `new-order-topic` in ActiveMQ.

   ```
   Topic: new-order-topic
   Sending new order message: {"orderId":3,"customerName":"Bob Johnson","pizzaType":"Veggie","pizzaStore":null,"destination":"789 Pine St","orderStatus":null}
   ```

   Upon receiving the new order message, the system tries to assign a drone for delivery. However, as there are no drones available at the moment, the system updates the order status to "Wait for shipping". The order request is then sent to drones and the system publishes this status update message.

   ```
   Received new order message: {"orderId":3,"customerName":"Bob Johnson","pizzaType":"Veggie","pizzaStore":null,"destination":"789 Pine St","orderStatus":null}

   Published order request message to drones: {"orderId":3,"customerName":"Bob Johnson","pizzaType":"Veggie","pizzaStore":null,"destination":"789 Pine St","orderStatus":null}

   Published order status message: {"orderId":3,"customerName":"Bob Johnson","pizzaType":"Veggie","pizzaStore":null,"destination":"789 Pine St","orderStatus":"Wait for shipping"}
   ```

   ```
   Order request delivery Message Received: {"orderId":3,"customerName":"Bob Johnson","pizzaType":"Veggie","pizzaStore":null,"destination":"789 Pine St","orderStatus":"Wait for shipping"}
   Processing the order now...
   ```

   The system then listens for this update message, updates the order status in the database, and returns the updated PizzaOrder object. When Bob Johnson requests to view his pizza order, the order he placed is shown with the status as "Wait for shipping".

   ```
   Received order status update message: {"orderId":3,"customerName":"Bob Johnson","pizzaType":"Veggie","pizzaStore":null,"destination":"789 Pine St","orderStatus":"Wait for shipping"}
   ```

2. **Scenario: Order with Available Drones**

   ```bash
   (base) jingjingyang@jingjings-MacBook-Pro ~ % curl -X POST -H "Content-Type: application/json" -d '{
     "id": "1",
     "name": "Drone 1",
     "capacity": 50,
     "gpsLocation": "Fleet Center",
     "status": "Idle",
     "url": "http://localhost:8011/api/drone1"
   }' http://localhost:8020/api/fleet/drones
   Drone added successfully%
   ```

   Emma Watson places an order for a Hawaiian pizza to be delivered to 321 Maple Ave. This is done via a POST request to the `/api/pizzaOrders` endpoint with the order details sent in JSON format.

   ```bash
   (base) jingjingyang@jingjings-MacBook-Pro ~ % curl -X POST -H "Content-Type: application/json" -d '{
     "customerName": "Emma Watson",
     "pizzaType": "Hawaiian",
     "destination": "321 Maple Ave"
   }' http://localhost:8070/api/pizzaOrders
   {"orderId":4,"customerName":"Emma Watson","pizzaType":"Hawaiian","pizzaStore":null,"destination":"321 Maple Ave","orderStatus":null}%
   ```

   When the order is placed, the system receives a POST request with the order details in JSON format. The system creates a new PizzaOrder object with the provided details and saves it to the database. The order details are then converted to a JSON string and sent as a message to the `new-order-topic` in ActiveMQ.

   ```
   Topic: new-order-topic
   Sending new order message: {"orderId":4,"customerName":"Emma Watson","pizzaType":"Hawaiian","pizzaStore":null,"destination":"321 Maple Ave","orderStatus":null}
   ```

   Upon receiving the new order message, the system finds a drone available for delivery. The drone is then dispatched to the destination address, and the system updates the order status to "In Transit" and publishes this status update message.

   ```
   Received new order message: {"orderId":4,"customerName":"Emma Watson","pizzaType":"Hawaiian","pizzaStore":null,"destination":"321 Maple Ave","orderStatus":null}

   Drone with ID 1 has been dispatched for order: {"orderId":4,"customerName":"Emma Watson","pizzaType":"Hawaiian","pizzaStore":null,"destination":"321 Maple Ave","orderStatus":null}

   Published order status message: {"orderId":4,"customerName":"Emma Watson","pizzaType":"Hawaiian","pizzaStore":null,"destination":"321 Maple Ave","orderStatus":"In Transit"}
   ```

   ```
   Drone with ID 1 is flying to 321 Maple Ave
   ```

   The system then listens for this update message, updates the order status in the database, and returns the updated PizzaOrder object. When Emma Watson requests to view her pizza order, the order she placed is shown with the status as "In Transit".

   ```
   Received order status update message: {"orderId":4,"customerName":"Emma Watson","pizzaType":"Hawaiian","pizzaStore":null,"destination":"321 Maple Ave","orderStatus":"In Transit"}
   ```

## Dependencies

For this project, we use Maven as a build tool and Spring Boot as the framework. The dependencies in the pom.xml include:

- `spring-boot-starter-data-jpa` for data layer access using JPA
- `spring-boot-starter-web` for web application creation
- `spring-boot-starter-test` for application testing
- `mysql-connector-java` version `8.0.23` for MySQL database connection
- `spring-boot-starter-thymeleaf` for server-side Java template engine
- `spring-boot-starter-activemq` for JMS messaging
- `activemq-all` version `5.16.5` for all ActiveMQ modules
- `netty-resolver-dns-native-macos` for DNS resolution on MacOS

This system uses Spring's REST Controller and Service annotations to expose APIs and implement the service logic, respectively.

## Note

These components are part of a larger system, and they interact with each other to function correctly. Make sure these components are properly set up and running to ensure the whole system functions correctly.
