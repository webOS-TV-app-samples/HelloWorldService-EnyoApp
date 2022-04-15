# Hello World Service (EnyoApp)

> The Enyo framework is not supported on webOS TV released since 2019 (webOS TV 4.5). However, if you want to keep using the Enyo framework, package Enyo libraries and resources into your app.

## About Code Sample

In this sample, we will create a simple HelloWorld app that demonstrates how to create a webOS TV service. This sample also demonstrates how to call the service from an Enyo app and display the response. We will be using the command line tool of the webOS SDK to create the app and service, as well as packaging and installing the app on the target device.

## Creating a Project

From webOS TV 4.0 SDK, we do not provide Moonstone templates of Enyo using Command Line Interface (CLI).

- For creating an Enyo-based app, refer to the [Enyo](https://github.com/enyojs) site.
- For more information about supporting Enyo and Enact, see [Enyo/Enact Guides](https://webostv.developer.lge.com/develop/enyo-enact-developer-guide/).

## Writing an App

We now have a skeleton project on which to build the HelloWorld app and service. Before you start writing an app, open appinfo.json and specify the app name at "id:" field. In this code sample, I set it to **com.yourdomain.helloworldservice**.

First, we create an app that calls the service. Using your favorite editor, open **source/views/views.js,** and replace the code with the code below.

The **enyo.LunaService** kind is provided by the **enyo-webos** library (which can be downloaded in the [Enyo repository](https://github.com/enyojs/enyo-webos) of GitHub) and is what you use to call webOS services. By default, the enyo-webos library is commented out of the **source/package.js**. You should modify the package.js file to use the enyo-webos library.

```javascript
enyo.kind({
  name: 'MyApp.MainView',
  kind: 'moon.Panel',
  title: 'Hello World',
  classes: 'moon main-view',
  components: [
    {
      name: 'service',
      kind: 'enyo.LunaService',
      service: 'luna://com.yourdomain.helloworldservice.service',
      method: 'hello',
      onComplete: 'onComplete',
    },
    {
      kind: 'moon.Scroller',
      fit: true,
      components: [
        {
          classes: 'moon-button-sample-wrapper',
          components: [
            {
              kind: 'moon.Button',
              content: 'Call Service',
              ontap: 'callService',
            },
            {
              name: 'nameInput',
              kind: 'moon.Input',
              placeholder: 'Enter your name',
            },
            {
              name: 'result',
              kind: 'moon.BodyText',
              content: 'Button not pressed yet.',
            },
          ],
        },
      ],
    },
  ],
  callService: function (inSender, inEvent) {
    // make sure a name is sent to the service
    var name = this.$.nameInput.get('value') || 'Mysterious Stranger';

    this.$.result.set('content', 'Calling service with: ' + name);
    this.$.service.send({ name: name });
  },
  onComplete: function (inSender, inResponse) {
    if (inResponse.returnValue) {
      this.set('title', inResponse.data);
      this.$.result.set('content', 'Service responded.');
    } else {
      this.$.result.set(
        'content',
        'Oooops!  There is a problem with this service.'
      );
    }
  },
});
```

## Writing a Service

Now create a service to an app in a separate directory. Create a new folder called **service**. You can use any name for the service folder. A service is made up of three files, which we add in order.

First, create a new file called **services.json** and insert the following code. This file defines our service and the methods available to be called on it.

```json
{
  "id": "com.yourdomain.helloworldservice.service",
  "description": "Hello World Service Example Service",
  "services": [
    {
      "name": "com.yourdomain.helloworldservice.service",
      "description": "Hello World Service Example Service",
      "commands": [
        {
          "name": "hello",
          "description": "Responds with the parameter given; echo service",
          "public": true
        }
      ]
    }
  ]
}
```

Next, create a new file called **package.json** and insert the following code.

```json
{
  "name": "com.yourdomain.helloworldservice.service",
  "main": "HelloWorldService.js"
}
```

Finally, create a new file called **HelloWorldService.js** and insert the following JavaScript code to execute the service. This creates the method that is called from the app.

```javascript
var Service = require('webos-service');
var service = new Service('com.yourdomain.helloworldservice.service');

service.register('hello', function (message) {
  message.respond({
    data: 'Hello, ' + message.payload.name + '!',
  });
});
```

## Packaging the App 

We are now ready to package our app and test it on the target device. Create the package using the following command.

<mark>ares-package app service</mark>

## Installing and Launching the App

Install the package on the target device using <mark>ares-install com.yourdomain.helloworldservice_0.0.1_all.ipk</mark>

After you install the app, you can launch the app by selecting the app icon on the Launcher bar or executing the following command.

<mark>ares-launch com.yourdomain.helloworldservice</mark>

After the app is running, enter your name in the text box, then click the **CALL SERVICE** button. The app calls the service with the name entered as a parameter and returns the "Hello" response.

![The result image of this code sample](https://webostv.developer.lge.com/download_file/view_inline/2118/)

## Do's and Don'ts

- **DO** match the base app ID with the service ID as in this example (com.yourdomain.helloworldservice).

- **DO** handle the onComplete event at least.

- **DON'T** expect this to work in a browser.
