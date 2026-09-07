# Low-Latency Trading System

A low-latency trading system built using **C++**, **WebSocket**, **MySQL**, and a web-based frontend. The project demonstrates real-time order submission, order matching, client communication, and persistent trade/order management.

## Overview

The system is designed around a client-server architecture:

* Users interact with the trading interface through a web browser.
* The frontend communicates with the C++ backend using WebSocket connections.
* Incoming buy and sell orders are processed by the trading engine.
* Orders are matched based on price and time priority.
* Pending and completed orders are maintained and updated in the MySQL database.
* Multiple connected clients receive real-time trade/order updates.

## System Architecture

The backend is divided into three primary components:

### 1. WebSocket Communication

The WebSocket layer handles communication between the browser clients and the backend server.

* Accepts incoming WebSocket connections.
* Maintains active client connections.
* Receives trading requests from clients.
* Places incoming requests into a thread-safe queue.
* Broadcasts relevant trading updates to connected clients.

### 2. Trade Processing Engine

The trade processing engine handles order matching.

Buy and sell orders are organized using C++ ordered containers based on their limit prices.

The matching process follows:

1. Receive a new buy or sell order.
2. Check the available orders on the opposite side of the market.
3. Attempt to match orders according to price priority.
4. For orders at the same price, maintain time-of-arrival priority.
5. Execute matching orders and remove fulfilled orders.
6. Add any partially or completely unfulfilled quantity to the appropriate pending-order queue.

This provides a simplified implementation of an electronic order-matching engine.

### 3. Database Management

MySQL is used to persist user and order information.

The database layer handles:

* Database connections.
* User information.
* Order records.
* Order status updates.
* Creation of required database tables.

Database operations are separated from the main trade-processing flow where appropriate to avoid unnecessarily blocking order processing.

## Tech Stack

* **C++** — Backend and trade-processing engine
* **WebSocket** — Real-time client-server communication
* **MySQL** — Persistent data storage
* **PHP** — Web application backend
* **JavaScript** — Frontend interaction
* **HTML/CSS** — User interface
* **Boost** — C++ networking and supporting functionality
* **Makefile** — Build automation

## Project Structure

```text
low-latency-trading/
│
├── frontend/             # Web-based trading interface
├── Submission/           # Web interface/source files
├── headers.hpp            # Shared C++ declarations
├── DatabaseHandler.cpp   # Database connectivity and operations
├── Order.cpp             # Order representation and processing
├── *.cpp                 # Backend trading components
├── *.hpp                 # Header files
├── makefile              # Build configuration
└── README.md             # Project documentation
```

## Requirements

Before running the project, install:

* C++ compiler with C++11 or later support
* MySQL Server
* MySQL Connector/C++
* Boost libraries
* PHP
* A web server such as Apache/XAMPP

### Ubuntu

Depending on your environment, required packages can be installed with:

```bash
sudo apt update
sudo apt install build-essential
sudo apt install libmysqlcppconn-dev
sudo apt install libboost-all-dev
sudo apt install apache2
sudo apt install php
```

## Setup

### 1. Configure MySQL

Create a MySQL database and user with the permissions required by the application.

The backend creates the required tables when the database connection is initialized.

### 2. Configure the Web Server

Place the `frontend` directory inside your web server's document root.

For XAMPP on Linux:

```text
/opt/lampp/htdocs/
```

### 3. Build the Backend

From the project root:

```bash
make
```

This compiles the C++ source files and generates the backend executable.

### 4. Start the Backend

Run:

```bash
./backend
```

The application will request the database server details:

```text
Enter Server IP address:
Enter Username:
Enter Password:
```

Enter the appropriate credentials for your local MySQL configuration.

### 5. Open the Web Interface

Start your Apache/web server and open:

```text
http://localhost/frontend/index.php
```

The exact URL may differ depending on your web-server configuration.

## How It Works

The basic workflow is:

```text
Web Browser
     │
     │ WebSocket
     ▼
C++ Backend
     │
     ├── WebSocket Handler
     │
     ├── Trade Processing Engine
     │
     └── Database Handler
              │
              ▼
           MySQL
```

When a client submits an order:

1. The order is sent from the browser to the backend through WebSocket.
2. The backend places the request into a thread-safe processing queue.
3. The trade engine evaluates the order against available opposite-side orders.
4. Matching orders are executed according to price/time priority.
5. Unfilled quantities remain as pending orders.
6. Relevant order information is persisted in MySQL.
7. Connected clients receive real-time updates.

## Concurrency

The backend uses multiple threads for different responsibilities, including:

* WebSocket connection handling
* Client communication
* Trade-request processing
* Database updates

Thread-safe queues and synchronization mechanisms are used to coordinate communication between these components.

## Security Note

This project is intended as an educational implementation of a simplified trading system.

For production use, additional security and reliability measures would be required, including:

* Parameterized SQL queries
* Secure credential management
* Authentication and authorization improvements
* Input validation
* TLS/WSS for encrypted WebSocket communication
* Robust error handling
* Transaction management
* Production-grade concurrency controls

## Disclaimer

This project is a simplified implementation intended for learning and experimentation. It is **not intended for production financial trading**.

## License

This project is provided for educational and development purposes.
