Vignette Software Design Specification
====
*Revision: 1.0, Last Edited: 02/19/2024*

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
   - The Relay Network, which is provided either by Steam Data Relay (SDR) for the Steam version, or directly by the user (Free/OSS).
   - The Coordination Server, which provides a way for users to join collaboration sessions as easy as sharing a link. The links are base64 encoded to include metadata of where the client should connect to and what type of connection should it be depending on the user's tier (direct for Free/OSS, sdr for Steam users).
- **Marketplace**: Provides a first-party gallery of extensions, assets, and many more for content creators, created by content creators. The Marketplace is vendor-agnostic, and is provided by us that also provides billing services for paid addons. (NB: It is worth noting that paid addons pay a 10% commission which should be deducted by the system automatically from their SKU price).
- **Passport**: Provides a centralised IDP (Identity Provider) for all of Vignette's services, including Enterprise clients. The IDP provides login via social networks and/or through email magic links. Our online services should be *password-free*.


## Design Decisions and Tradeoffs

We have employed a shared-code architecture with the web and the client to increase developer velocity and require minimal learning curve to onboarding new developers and contributors. This comes with an added benefit of being able to use the same toolchain for most of our projects.

Vignette is also promoting modularity, as the main architecture of the program revolves around providing an all-in-one suite for content creators; therefore, even the main features of the application should be implemented like extensions.

Similarly, we have also chosen TypeScript and web technologies in order to reduce engineering time required to develop platform-specific code, however, as the project matures, we will be contributing our effort upstream in order to make Vignette reach other audiences, such as React Native Desktop.