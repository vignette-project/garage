Vignette Software Design Specification
====
*Revision: 1.5, Last Edited: 02/19/2024*

Vignette is a open-source, cloud-first streaming toolkit for content creators, leveraging Artificial Intelligence, Machine Learning, and Web Technologies to provide a flexble and modular creative platform.

## Goals and objectives

Vignette aims to do the following that most competitors usually shy away from or usually would hide behind a paywall. In essence our goals are the following:

- To provide a consistent, all-in-one suite for VFX, Tracking, and Scene Composition. User should not require to download seperate programs just to do their content.
- To establish an open ecosystem for content creators: allowing them to create:
    - Plugins to extend the functionality of the program.
    - Additional Assets to add more variety to their streams.
    - Custom providers and integrations for their own tools and hardware.

- To provide a collaborative platform that requires minimal upfront cost to adopt, migrate and learn.

With these goals in mind, Vignette is designed to mostly cater to most of our audience's requirements, which revolves around live streaming.

## System Architecture

Vignette leverages advances with Web Technologies in order to achieve a consistent experience between platforms. the components is comprised of the following:

- **Workbench**: The consumer-facing component of the application. The Workbench is designed with dockable tabs and sports mainly a two-column, two-row layout. The Workbench is made with React Native to allow sharing of components with the web-facing side of the project.
- **Scene**: Provides the framework for the overall logic of the program. It manages all the necessary primitives and objects and provides a consistent API to allow the Workbench to interact with as well as third-party integrations (Plugins). This component is made with purely TypeScript, uses extensive web standards like [WebGPU](https://developer.mozilla.org/en-US/docs/Web/API/WebGPU_API), and avoids platform-specific code when possible.
- **Machine Learning Runtime**: Provides a runtime for machine learning models to run and deploy in the program. Vignette primarily uses MediaPipe and Tensorflow Lite to run and deploy our first-party models provided with the programs.
- **Native Runtime**: The framework that interacts with the native environment. This is where the platform-specific code resides on. Vignette is primarily designed to run in an environment like NW.js and React Native to reduce the needed boilerplate.

Being a cloud-first program, Vignette also comprises of the following web services:

- **Web**: Provides the marketing, documentation, and pricing details. This is our consumer facing side and this is the usual entrypoint for any interactivity with the system.
- **The Multiplayer Network**: Provides the collaborative environment (also known as multiplayer) for Vignette. It is usually composed of two things:
   - The Relay Network, which is provided either by Steam Data Relay (SDR) for the Steam version, or through dedicated servers hosted by Vignette for paying customers (Pro).
   - The Coordination Server, which provides a way for users to join collaboration sessions as easy as sharing a link. The links are base64 encoded to include metadata of where the client should connect to and what type of connection should it be depending on the user's tier (direct for Free, dedicated for Pro/Enterprise, sdr for Steam users).
- **Marketplace**: Provides a first-party gallery of extensions, assets, and many more for content creators, created by content creators. The Marketplace is vendor-agnostic, and is provided by us that also provides billing services for paid addons. (NB: It is worth noting that paid addons pay a 10% commission which should be deducted by the system automatically from their SKU price).
- **Passport**: Provides a centralised IDP (Identity Provider) for all of Vignette's services, including Enterprise clients. The IDP provides login via social networks and/or through email magic links. Our online services should be *password-free*.


## Requirements

Vignette has several functional and non-functional requirements in order to achieve its necessary goals.

### Functional Requirements

| Area | User Story | Priority |
| -------- | -------- | -------- |
| Web | As a user, I want to be able to publish my work to be used by other people in a marketplace.      | :red_circle: Must Have     |
| Client | As a user, I want to be able to host a collaboration session as if it was a Multiplayer session in a game to interact with my fellow content creators. I should be able to invite them just by sharing a link. | :red_circle: Must Have |
| Client | As a user, I want to be able to do more out of my copy of the program, by being able to make or download assets that extend the program's capabilities | :red_circle: Must Have |
| Client | As a user of a competing program, I am frustrated that my current toolkit can impact my PC's performance, costing me some of my gameplay. I want the software to be minimal and performant as much as possible. | :red_circle: Must Have |
| Client | As a user, I want to be able to control how the tracking works, as well as a way to assign the parameters to other parts of my model or in the scene. | :red_circle: Must Have |
| Web | As a prospective user, I want to be able to explore the capabilities of the program in the website, as well as having the pricing upfront so I know what I'm investing with. | :red_circle: Must Have |

### Non-functional Requirements


| Area | User Story | Priority |
| -------- | -------- | -------- |
| Client     | As a user, I get frustrated how confusing most programs similar to this are. I should be able to know where to go just by first glance.     | :red_circle: Must Have     |
| Client | As a user, I do not like other programs have arbitrary limits with collaboration. I should be able to host a large one to my heart's content. | :large_orange_diamond: Not Required but Preferable |
| Client | As a user, I shouldn't be required to log in to services if I don't need them. I should be able to manage my own content when I wish to. | :large_orange_diamond: Not Required but Preferable |
| Web | As a user, regardless if I'm an enterprise user or not, I should be able to manage my account and log in on one single pane of glass. | :red_circle: Must Have |
| Client | As a user, I should be able to have an option to use a dedicated server for my collaboration sessions when possible. I do not mind paying for it or hosting it myself. | :large_orange_diamond: Not Required but Preferable. |

## Design Decisions and Tradeoffs

We have employed a shared-code architecture to increase developer velocity and require minimal learning curve to onboarding new developers and contributors. This comes with an added benefit of being able to use the same toolchain for most of our projects.

Vignette is also promoting modularity, as the main architecture of the program revolves around providing an all-in-one suite for content creators; therefore, even the main features of the application should be implemented like extensions.

Similarly, we have also chosen TypeScript and web technologies in order to reduce engineering time required to develop platform-specific code, however, as the project matures, we will be contributing our effort upstream in order to make Vignette reach other audiences, such as React Native Desktop.
