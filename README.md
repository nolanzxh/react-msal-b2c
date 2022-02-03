# README

Azure AD B2C is a cost effective identity provider covering social and enterprise logins but it can be awekward to integrate with - its documentation is currently not great and using it involves rooting around across multiple samples, the ADAL library, and the MSAL library.

This module is a wrapper over MSAL for Azure AD B2C, who simplifies the use of MSAL.

This module works with [msal@1.4.15](https://www.npmjs.com/package/msal/v/1.4.15)!

## Installation

If you are using npm:

    npm install react-azure-adb2c --save

Or if you are using yarn:

    yarn add react-azure-adb2c

## Initializing the Library

You'll first need to load the module and pass some configuration to the library. Normally this would go in your index.js file:

    import authentication from 'react-azure-adb2c';
    authentication.initialize({
        // optional, will default to this
        instance: 'https://login.microsoftonline.com/tfp/', 
        // your B2C tenant
        tenant: 'myb2ctenant.onmicrosoft.com',
        // the policy to use to sign in, can also be a sign up or sign in policy
        signInPolicy: 'mysigninpolicy',
        // the policy to use for password reset
        resetPolicy: 'mypasswordresetpolicy',
        // the the B2C application you want to authenticate with (that's just a random GUID - get yours from the portal)
        applicationId: '75ee2b43-ad2c-4366-9b8f-84b7d19d776e',
        // where MSAL will store state - localStorage or sessionStorage
        cacheLocation: 'sessionStorage',
        // the scopes you want included in the access token
        scopes: ['https://myb2ctenant.onmicrosoft.com/management/admin'],
        // optional, the redirect URI - if not specified MSAL will pick up the location from window.href
        redirectUri: 'http://localhost:3000',
        // optional, the URI to redirect to after logout
        postLogoutRedirectUri: 'http://myapp.com'
    });
    
## Authenticating When The App Starts

If you want to set things up so that a user is authenticated as soon as they hit your app (for example if you've got a link to an app from a landing page) then, in index.js, wrap the lines of code that launch the React app with the _authentication.run_ function:

    authentication.run(() => {
      ReactDOM.render(<App />, document.getElementById('root'));
      registerServiceWorker();  
    });

## Triggering Authentication Based on Components Mounting (and routing)

If you want to set things up so that a user is authenticated as they visit a part of the application that requires authentication then the appropriate components can be wrapped inside higher order components that will handle the authentication process. This is done using the _authentication.required_ function, normally in conjunction with a router. The example below shows this using the popular react-router:

    import React, { Component } from 'react';
    import authentication from 'react-azure-adb2c'
    import { BrowserRouter as Router, Route, Switch } from "react-router-dom";
    import HomePage from './Homepage'
    import MembersArea from './MembersArea'
    
    class App extends Component {
      render() {
        return (
          <Router basename={process.env.PUBLIC_URL}>
            <Switch>
              <Route exact path="/" component={HomePage} />
              <Route exact path="/membersArea" component={authentication.required(MembersArea)}>
            </Switch>
          </Router>
        );
      }
    }

## Getting the Access Token

Simply call the method _getAccessToken_:

    import authentication from 'react-azure-adb2c'

    // ...

    const token = authentication.getAccessToken();

## Signing Out

To sign out:

    import authentication from 'react-azure-adb2c'

    // ...

    authentication.signOut();

## Thanks

To build this I made fork of [react-azure-adb2c](https://github.com/JamesRandall/react-azure-adb2c) module which is deprecated, it was last published 4 years ago.