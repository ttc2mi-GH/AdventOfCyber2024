******************************************************************************************************************************
                    Day 24: You can't hurt SOC-mas, Mayor Malware!
******************************************************************************************************************************

The basics of the MQTT protocol
How to use Wireshark to analyze MQTT traffic
Reverse engineering a simple network protocol

MQTT stands for Message Queuing Telemetry Transport.

It is a language very commonly used in IoT devices for communication purposes. It works on a publish/subscribe model, where any client device can publish messages, and other client devices can subscribe to the messages if they are related to a topic of interest. An MQTT broker connects the different clients, publishing and subscribing to messages.

MQTT Clients: MQTT clients are IoT devices, such as sensors and controllers, that publish or subscribe to messages using the MQTT protocol. For example, a temperature sensor can be a client that publishes temperature sensors at different places. An HVAC controller can also act as a client that subscribes to messages from the temperature sensor and turns the HVAC system on or off based on the input received.

MQTT Broker: An MQTT broker receives messages from publishing clients and distributes them to the subscribing clients based on their preferences.

MQTT Topics: Topics are used to classify the different types of messages. Clients can subscribe to messages based on their topics of interest. For example, a temperature sensor sending temperature readings can use the topic of “room temperature”, while an HVAC controller would subscribe to messages under the topic of “room temperature”. However, a light sensor can publish messages with the topic “light readings”. An HVAC controller does not need to subscribe to this topic. On the other hand, a light controller would subscribe to “light readings” but not to the topic of “room temperature”.



******************************************
                Investigation
******************************************        

~/Desktop/MQTTSIM/










*****************************************************
                Commandline Arguments
*****************************************************











*****************************************************
                Section 1
*****************************************************

*****************************************************
                Section 2
*****************************************************

*****************************************************
                Section 3
*****************************************************