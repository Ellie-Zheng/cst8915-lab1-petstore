# CST8915 Lab 1: Algonquin Pet Store on Azure VM

- **Student Name**: Ellie Zheng
- **Student ID**: 041078727
- **Course**: CST8915 Full-stack Cloud-native Development
- **Semester**: Fall 2026

---

## Demo Video

🎥 [Watch Demo Video](https://youtu.be/8jmjHDt9OSo)

---

## Technical Explanations

### Order Service (Node.js)

The Order Service receives orders from the Store Front through an HTTP `POST /orders` endpoint. Built with Express, `amqplib`, and CORS middleware, it acts as an order ingestion service between the client (browser-based Store Front) and RabbitMQ. This service is responsible for accepting and queuing orders, but it does not fulfill or ship them.
Upon receiving an order, it converts the request body to JSON and publishes it to a durable `order_queue` through a local RabbitMQ connection. The message is marked persistent, and the service waits for RabbitMQ to confirm it before returning a success response. If queuing fails, it returns an error instead.

### Product Service (Rust)

The Product Service provides the pet store’s product catalog. Built with Rust and the Warp web framework, it exposes a `GET /products` endpoint that returns a JSON array of product objects (containing ID, name, and price). In this lab, the product information is written directly in the source code rather than stored in a database.
In the microservices architecture, this service is responsible for product information and does not handle orders.  The browser-based Store Front queries `GET /products` directly via HTTP, with CORS middleware enabled to support cross-origin requests. While this service handles catalog read operations, order ingestion flows independently through the Order Service and RabbitMQ.

### Store Front (Vue.js)

The Store Front is the single-page application for customer interaction, built with Vue.js. It renders the product catalog, handles item selection and quantity inputs, and dynamically calculates order totals. Vue’s component data and computed property keep the displayed selection and total in sync with the customer’s input.
Within the microservices architecture, the Store Front acts as the presentation layer connecting users to the underlying backend services. When loaded in the browser, it sends a `GET` request to the Product Service on port `3030` to populate the catalog. When the customer places an order, it sends a JSON `POST` request with the selection details and total price to the Order Service on port `3000`. When deployed on an Azure VM, these backend endpoint URLs must be configured using the VM's public IP address so client browsers can successfully route API traffic.

---

## Challenges and Learnings

I got stuck while following the Store Front setup instructions, specifically the step that asked me to edit two API URLs in `src/components/OrderForm.vue`. At first, I actually got confused and spent time looking for the file inside the Azure portal. I completely forgot for a second that the code was living on my Linux VM, not in the portal interface! Once I realized I needed to navigate to the file on the VM, I still felt lost: I hadn't touched Linux commands in about three years, so my brain just went blank on where to start.
Luckily, my husband (who's a DevOps specialist) took a look at the instructions and showed me how to locate the two strings in the file right away.
It was a great reality check for me. It really showed how fast practical skills can fade when you don't use them every day, and it definitely motivated me to get more hands-on practice with Linux CLI navigation.  

---

## Acknowledgments

Thank you to my husband Leo for helping me locate the two API URLs in OrderForm.vue when I was stuck.
