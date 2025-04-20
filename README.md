# cpp
// nasdaq_retriever.cpp

#include <blpapi.hpp>
#include <blpapi_sessionoptions.h>
#include <blpapi_eventdispatcher.h>
#include <blpapi_message.h>
#include <blpapi_element.h>
#include <blpapi_name.h>
#include <blpapi_datatype.h>

#include <iostream>
#include <string>

// Bloomberg API credentials
const std::string HOST = "localhost"; // or your Bloomberg server IP
const std::string PORT = "8194"; // default port
const std::string USERNAME = "your_bloomberg_username";
const std::string PASSWORD = "your_bloomberg_password";

// NASDAQ Index ticker
const std::string NASDAQ_INDEX_TICKER = "CCMP";

int main() {
    // Set up Bloomberg API session options
    blpapi::SessionOptions sessionOptions;
    sessionOptions.setServerHost(HOST.c_str());
    sessionOptions.setServerPort(PORT.c_str());
    sessionOptions.setAuthenticationOptions(
        blpapi::AuthenticationOptions()
            .setUsername(USERNAME.c_str())
            .setPassword(PASSWORD.c_str())
    );

    // Create a Bloomberg API session
    blpapi::Session session(sessionOptions);
    if (!session.start()) {
        std::cerr << "Failed to start Bloomberg API session." << std::endl;
        return 1;
    }

    // Create a service for market data
    blpapi::Service service = session.getService("marketData");

    // Create an event dispatcher
    blpapi::EventDispatcher dispatcher;

    // Create a request for the NASDAQ Index
    blpapi::Request request = service.createRequest("historicalData");
    request.set("security", NASDAQ_INDEX_TICKER.c_str());
    request.set("fields", "PX_LAST"); // retrieve last price
    request.set("startDate", "2022-01-01"); // adjust date range as needed
    request.set("endDate", "2022-01-31"); // adjust date range as needed
    request.set("interval", "1D"); // daily intervals
    request.set("overrideOption", "OVERRIDE_OPTIONS_G GAP_FILL_NULL_EOD_LAST_VALUE");

    // Send the request and dispatch events
    session.sendRequest(request, dispatcher);
    while (dispatcher.nextEvent()) {
        if (dispatcher.eventType() == blpapi::Event::RESPONSE) {
            blpapi::Message msg = dispatcher.message();
            if (msg.hasElement("securityData")) {
                blpapi::Element securityData = msg.getElement("securityData");
                if (securityData.hasElement("security")) {
                    blpapi::Element security = securityData.getElement("security");
                    if (security.hasElement("ticker")) {
                        std::string ticker = security.getElementAsString("ticker");
                        if (ticker == NASDAQ_INDEX_TICKER) {
                            // Extract and print the last price
                            if (securityData.hasElement("fieldData")) {
                                blpapi::Element fieldData = securityData.getElement("fieldData");
                                if (fieldData.hasElement("PX_LAST")) {
                                    double lastPrice = fieldData.getElementAsFloat64("PX_LAST");
                                    std::cout << "NASDAQ Index (" << NASDAQ_INDEX_TICKER << ") Last Price: " << lastPrice << std::endl;
                                }
                            }
                        }
                    }
                }
            }
        }
    }

    // Stop the Bloomberg API session
    session.stop();

    return 0;
}
