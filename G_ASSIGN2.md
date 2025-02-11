# Group Assignment 2 / Final Project - Communicating Using Sockets

In this capstone project you will write software that communicates using IP protocols over a network. You may write the application in any language you like, but Python is a strong recommendation.

You will create a simple networked application in Python that uses *both* TCP and UDP protocols. The application consists of a **Server** and a **Client**. You are free to select the “theme” (e.g., chat, mini-game, key-value store) as long as it satisfies the specifications below. Your code will illustrate how TCP and UDP differ, while letting you practice socket programming and concurrency.

## Steps

1. Decide on the **application** you want to write. You should try to come up with an application that can use both UDP and TCP in a single application. A few examples are:

    * A chat server - UDP for short messages such as status updates, TCP for long-lived or temporary reliable connections for messages
    * An online game - TCP as an "orchestrator" with UDP carrying realtime messages
    * A remote control client - UDP for control messages, TCP for things like sending/receiving files. (Be careful with this one - security matters!)
  
    If you find you can't think of a good application that uses both protocols, you can instead opt to write two separate applications&ndash;one using UDP and one using TCP&ndash;but this will require extra work since you'll need to duplicate your efforts (you need *both* a server and client for each protocol in that case.)

    > Use some common sense here. A simple "echo" server, that just echoes TCP and UDP packets back to you, is not a suitable project; however, you do not need to implement a full-blown detailed protocol. If you are unsure as to whether your project would be an appropriate scope please feel free to reach out to me.
    >
    > Remember that you have only about 3 weeks to plan and write the code for this project, so don't be too ambitious, but at the same time don't cut too many corners!

1. Discuss and develop the **protocol specification**. You can use various Internet RFCs - for example the [Telnet](https://datatracker.ietf.org/doc/html/rfc854) protocol, the [original HTTP 1.0](https://datatracker.ietf.org/doc/html/rfc1945) protocol or the [IRC](https://datatracker.ietf.org/doc/html/rfc1459) chat protocol - for inspiration and guidance on how protocol descriptions are written. 

    Your specification should include:

    * A short high-level description of your application and its function.
    * Which **port(s)** your application will listen on/connect to. You're free to choose any reasonable port above 1024, but you may want to avoid common ports used for things like Flask apps (`5000`), some React apps (`3000`) and so on. 
    * A description of each type of message your protocol defines. Include the message's format and what response(s) are expected (if any) for that message.
  
    > A handy tip is to use the common terms defined in [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119) when describing your protocol. In particular:
    >
    > * Something indicated as MUST or MUST NOT indicates aspects of the protocol that will be considered an error if not followed.
    > * SHOULD and SHOULD NOT are strong recommendations; not following them can lead to undesired behavior, unexpected outcomes or inefficiencies, but it is not strictly violating the protocol to not observe these recommendations.
    > * MAY refers to aspects of the protocol that are up to the user whether to follow. 

    You don't need to go overboard - the goal is simply to come up with a very simple proof of concept. While you should implement very basic error checking and protocol conformance validation, you don't need to go out of your way to check every possible edge case. Consider your protocol definition to be a guide for someone else who might want to write a program that can interact with your code - either as a replacement server or client.

    There is no prescribed length - your protocol description should be as long or short as it needs to be to cover the important aspects of your protocol. For simple protocols a single page may be sufficient.

    > Your protocol can be either binary or text-based. The distinction is covered in class. A text protocol is likely to be easier to implement as you can take advantage of the line-buffering capability of modern programming languages.

2. Implement a **server** and a **client** that follows the protocol you specified. 

    You may do this in any language your team feels most comfortable with. Python, C# and Java all have strong support for socket programming, including the ability to treat sockets as line-buffered files. 

3. **Test** your code, firstly by running both the client and server locally, and then by using the VPN service available in class to use the client to connect to another group member's computer running the server. 

    > You can even do this remotely - as long as you both connect to the VPN and know your IP addresses, you can connect via the VPN.

4. Prepare a short (5-7 minute) **presentation** about your program. 

    To save time, you don't need to follow the usual format of 1Q/midterm/final presentations. Instead, you should focus on your project's overall goal, discuss your protocol itself, and then demonstrate your code working. You can assume your audience is technically minded, so you don't need to be as careful with overuse of technical terminology.

    You will present this presentation during the final session of class (March 6th).

## Assignment Requirements

The following are the high-level requirements for this assignment:

* You should strive to use built-in Python libraries - `socket`, `asyncio`, and so on. Third-party libraries may help ease some of the burden, but it is beneficial for you to learn to write socket programs directly. 
* Use **threading** to allow your TCP service to accept multiple connections.
  * We'll have some examples of this in class.
  * This also specifies that your server program should be fully autonomous - if you're writing a chat server, you should be able to connect *two* clients to it and those two clients should be able to communicate, but the server code itself need not (and should not) provide its own ability to chat.
* Your code should handle the most common error cases - for UDP, you should take appropriate steps to handle lost packets (if it's necessary in the first place to handle them); for TCP, you should handle Connection Refused, Connection reset by peer, and connection timeout properly.
* Use appropriate `print` statements or Python `logging` to print appropriate messages to the console - in particular for the server side.

## Submission

Your submission must include:

* Your protocol document - Word or PDF format
* Your source code for your server and client applications
* A copy of or link to your presentation

The assignment final submission must be made by your group **by 11:59 PM on March 7th** - the last day of the block.

## Scoring Rubric

This assignment is worth 250 points. Points are assigned as follows:

| Item | Points | Penalties |
|-|-|-|
| Provide a written document describing the selected project domain and a technical description of the protocol. | 75 | Point loss for missing elements from the protocol document. |
| Successfully implement a client and server application implementing the defined protocol. | 100 | Point loss for non-working code, incorrect implementation or other significant errors.
| Present successfully working code and presentation in class. | 75 | Will be graded using the standard CS presentation rubric, without client sensitivity (those points are moved to the technical content section). | 
