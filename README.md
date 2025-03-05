
High-Performance Order Execution & Management System
For GoQuant Recruitment - Deribit Test Integration

*Table of Contents*
Introduction
System Overview
Technology Stack
Setup Instructions
Order Management Functions
WebSocket Implementation
Performance Optimization
Latency Benchmarking
Testing & Debugging
Deliverables

1. Introduction
This project is a high-performance order execution and management system for trading on Deribit Test. It supports order placement, modification, cancellation, and real-time market data streaming using WebSockets.

2. System Overview
The system is designed to:

Place, modify, cancel orders via Deribit API.
Stream real-time market data using WebSockets.
Optimize for low latency to ensure efficient trading.
Measure performance and identify bottlenecks.

3. Technology Stack
Component	Technology Used
Language	C++
API Handling	cURL, JSON
WebSocket	WebSocket++
Performance Optimization	Multi-threading, efficient data structures
Debugging & Logging	Standard output, error handling
4. Setup Instructions
1. Install Dependencies
Run the following command in Linux:

sh
Copy
Edit
sudo apt update && sudo apt install libcurl4-openssl-dev libwebsocketpp-dev libboost-all-dev

2. Create a Deribit Test Account
Sign up at Deribit Test.
Navigate to API Keys and generate API credentials.
Store your API_KEY and API_SECRET securely.

5. Order Management Functions
1. Place Order
This function submits an order via the Deribit API.

cpp
Copy
Edit
#include <iostream>
#include <curl/curl.h>
#include <json/json.h>

std::string API_KEY = "your_api_key";

std::string placeOrder(const std::string &instrument, const std::string &side, double amount, double price) {
    CURL *curl = curl_easy_init();
    if (!curl) return "Error initializing cURL";

    std::string url = "https://test.deribit.com/api/v2/private/" + side;
    std::string postData = "{\"instrument_name\": \"" + instrument + "\", \"amount\": " + std::to_string(amount) +
                           ", \"price\": " + std::to_string(price) + "}";

    curl_easy_setopt(curl, CURLOPT_URL, url.c_str());
    curl_easy_setopt(curl, CURLOPT_POSTFIELDS, postData.c_str());
    curl_easy_setopt(curl, CURLOPT_HTTPHEADER, {"Authorization: Bearer " + API_KEY});
    
    std::string response;
    curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, [](void *ptr, size_t size, size_t nmemb, std::string *data) {
        data->append((char *)ptr, size * nmemb);
        return size * nmemb;
    });
    curl_easy_setopt(curl, CURLOPT_WRITEDATA, &response);
    
    curl_easy_perform(curl);
    curl_easy_cleanup(curl);
    
    return response;
}

int main() {
    std::string response = placeOrder("BTC-PERPETUAL", "buy", 1.0, 50000.0);
    std::cout << "Order Response: " << response << std::endl;
    return 0;
}
Other Functions: Modify the API URL for:

Cancel Order: https://test.deribit.com/api/v2/private/cancel
Modify Order: https://test.deribit.com/api/v2/private/edit
Get Orderbook: https://test.deribit.com/api/v2/public/get_order_book
6. WebSocket Implementation
WebSocket integration is used for real-time market data streaming.

cpp
Copy
Edit
#include <iostream>
#include <websocketpp/config/asio_no_tls_client.hpp>
#include <websocketpp/client.hpp>

typedef websocketpp::client<websocketpp::config::asio_client> client;

void on_message(websocketpp::connection_hdl hdl, client::message_ptr msg) {
    std::cout << "Market Data: " << msg->get_payload() << std::endl;
}

int main() {
    client wsClient;
    wsClient.init_asio();

    websocketpp::lib::error_code ec;
    client::connection_ptr con = wsClient.get_connection("wss://test.deribit.com/ws/api/v2", ec);
    if (ec) {
        std::cout << "Connection Error: " << ec.message() << std::endl;
        return -1;
    }

    wsClient.set_message_handler(&on_message);
    wsClient.connect(con);
    wsClient.run();

    return 0;
}
7. Performance Optimization
1. Low-Latency Design Considerations
Efficient Data Structures: Use std::vector instead of std::list.
Multi-threading: Use std::thread for API requests.
WebSocket Connection Persistence: Avoid frequent reconnections.
8. Latency Benchmarking
To measure API response time:

cpp
Copy
Edit
#include <iostream>
#include <chrono>

auto start = std::chrono::high_resolution_clock::now();
// Execute API call here...
auto end = std::chrono::high_resolution_clock::now();
std::cout << "Latency: " << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count() << " μs" << std::endl;
9. Testing & Debugging
Test Cases
Test Case	Expected Output
Place Order	Order ID returned
Modify Order	Updated order details
Cancel Order	Order removed from orderbook
WebSocket Streaming	Live market data displayed
Debugging Techniques
Use std::cerr for error messages.
Implement log files for tracking API responses
