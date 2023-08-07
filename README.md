# Project information

#### Project summary

The main objective of this project is to build a service through which clients can share data by the means of BLE beacon technology. For this purpose two mobile applications on React Native – one which saves beacon identifiers along the data it is going to transmit, to a centralized server database, and another one which delivers that data to all nearby to the beacon, smartphone devices. To handle the necessary business logic and communication between the two mobile applications a RESTful service was built on Express.js.

For more detailed explanation please go to [Project structure](#project-structure)

#### What is BLE beacon?

Bluetooth Low Energy beacons are compact and portable devices which broadcast their hardware identifiers, to nearby devices through Bluetooth protocol. Beacons can be either USB or battery powered, with most manufacturers giving an estimate battery life of 1-48 months depending on beacon's configuration. Because of the low-cost, easy integration, lowered impact on smartphone's battery life, and the good accuracy they provide, BLE beacons are good alternative to other technologies used for proximity based applications such as GPS and Wi-Fi RTT.

# Project structure

Summary of the project's structure and workflow, can be seen, in the diagram below:![link](https://raw.githubusercontent.com/sashko9807/beacon-personalized-content-delivery-service/main/diagram.svg)

## BeaconMS

[Source code](https://github.com/sashko9807/BeaconMS) &nbsp; [APK Download](https://github.com/sashko9807/BeaconMS/releases/tag/v1.0.0)

_Used technologies: React Native, Redux Toolkit, RTK Query, React Hook Form_  
_Supported OS: Android_

BeaconMS short for _Beacon Managment System_ is the application through which user can add edit or remove data to be transmitted with the beacon. As of now the types of data that can be transmitted via beacon are: Plain-text, image and http link.

The application provides:

- Usage of RTK Query to send queries to the backend server, and cache the response to reduce server calls.
- Client side input validation via React Hook Form
- Authenticating system via email and password
- Authorization via JWT protocol. The returned from the backend Access and Refresh tokens are saved in the global application state via Redux Toolkit. These tokens are also used to keep track of user's session
- Scanning for nearby beacons to make user's experience easier when saving beacon's data to the database.

## BeaconScanner

[Source code](https://github.com/sashko9807/BeaconScanner) &nbsp; [APK Download](https://github.com/sashko9807/BeaconScanner/releases/tag/v1.0.0)

_Used technologies: React Native, Realm, Axios, Firebase Cloud messaging_  
_Supported OS: Android_

BeaconScanner is the application through which user receives the personalized data transmitted by the beacon.

The application provides:

- First-boot only onboarding screens. When application is first launched user has to go through a small setup in which he grants the necessary permissions for the application to function, as well as fetching the already existing data about the beacons and the data they transmit from the server database via Axios.
- Once downloaded from the server database, the beacon's unique identifiers, along the information they will transmit is saved locally to the device, via a local database called Realm.js, which would allow for the application to remain functional if there is no active internet connection. If the client's device is connected to cellular network, and the transmitted by the beacon data is image, the reference to the image is saved in the local databse. To prevent any additional data charge, the application wont download that image automatically. Instead it will notify the client that the BeaconScanner's data is out of sync, and give the user an option to either download the image via cellular network, or connect to Wi-Fi network first.

- In order to ensure application's local database is synchronized with the server database, whenever beacon data update is initiated from BeaconMS(or via API calls), a silent push notification is sent through Firebase Cloud Messaging, to all devices having BeaconScanner installed and running either in foreground or background.

## RESTful service

[Source code](https://github.com/sashko9807/beacon-restful-service)

_Used technologies: Express.js, Mongoose, Sendgrid, Firebase Cloud messaging_  
_Supported OS: Any OS supporting Node.js enviroment_

Handles the bussiness logic necessary to save beacon's data as well as the logic establishing communication between **BeaconMS** and **BeaconScanner**

The service provides:

- Bussiness logic to handle everything related to JWT authorization. This includes generating tokens, validating them, and decoding the information they include.
- Necessary endpoints to handle the logic needed to save,update or delete beacons data from the server database, as well as validating input coming from client.
- Sending silent push notifications to all BeaconScanner clients, through Firebase Cloud Messaging service.
- Sending emails through SendGrid API._(Used only for sending new passwords if user has forgotted his old password - **BeaconMS** only )_
