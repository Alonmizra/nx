---
title: 'Use Storybook with Nx React Native'
slug: 'use-storybook-with-nx-react-native'
authors: ['This member-only story is on us. Upgrade to access all of Medium.']
cover_image: '/blog/images/2022-04-25/1*64nWVfUBihlYTLGWOvnc1g.png'
tags: [nx, release]
---

In my previous [blogs](https://emilyxiong.medium.com/share-code-between-react-web-react-native-mobile-with-nx-fe5e22b5a755) _(see links at the end)_, I wrote about how to develop Nx React Native applications. However, as developers, we are constantly searching for ways to make the developer experience better.

This blog will show how to add Storybook to Nx React Native applications. With Nx, you don’t need to go through [this long guideline](https://storybook.js.org/tutorials/intro-to-storybook/react-native/en/get-started/) to set up the Storybook, you can quickly get it running.

Example Repo:

[

### GitHub - xiongemi/studio-ghibli-search-engine: A search engine to search films and characters under…

### This project was generated using Nx. 🔎 Smart, Extensible Build Framework This app is a search engine for Studio…

github.com

](https://github.com/xiongemi/studio-ghibli-search-engine?source=post_page-----2ddd8c010eda--------------------------------)

Storybook:

![](/blog/images/2022-04-25/1*bDKKjnrt2D6XIBDnWN1z2Q.avif)
_Storybook View (left: Android, right: iOS)_

## Setup

First, you need to add `@nrwl/storybook` to your existing Nx React Native workspace:

```shell
**_\# npm_**
npm install @nrwl/storybook --save-dev**_\# yarn_**
yarn add --dev @nrwl/storybook
```

Then you need to generate the storybook configuration for your app or lib:

```
nx g @nrwl/react-native:storybook-configuration **<your app or lib>**
```

As shown in the example below, 3 folders got generated:

- `.storybook` at workspace root
- `.storybook` in your app or lib
- `storybook` in your app (Note: this folder is for creating the Storybook UI component. It will only be created for the app, you will not see this for lib.)

![](/blog/images/2022-04-25/1*q1sX4VQKdRzQpye6Qcs2Ow.avif)

If you choose to automatically generate `*.stories` file, you should see the default story looks like below:

To gather the stories you created, run the command:

```
nx storybook **<your app or lib>**
```

You should see in the terminal saying:

```
Writing to **<your workspace>/.storybook/story-loader.js**
```

In your `<your workspace>/.storybook/story-loader.js`, it should list your stories created under your app or lib similar to the below example:

Also, notice that in your app’s main file, the import of the App changed to `storybook/toggle-storybook`:

```
import App from './storybook/toggle-storybook';
```

### View Storybook for App

To view the storybook on the simulator/emulator/device, start the app like you usually do:

```
**_\# iOS_**
nx run-ios <your app>**\# Android**
nx run-android <your app>
```

In your simulator/emulator/device, open the Debug Menu by entering `d` in terminal. You should see the menu option Toggle Storybook in the Debug Menu:

![](/blog/images/2022-04-25/1*aziO6KSwVhtXWwfyADGbAA.avif)
_Screenshot of Debug menu (left: Android, right: iOS)_

When switching on the toggle, you should see the list of your component stories:

![](/blog/images/2022-04-25/1*KYn3sPUpBU_ewRh2zJ7niQ.avif)
_Storybook View (left: Android, right: iOS)_

### View Storybook for Lib

Note: the storybook can only be viewed inside an app. To view the storybook for lib in the workspace, you need to first set up the storybook for an app in the workspace.

Then run the command:

```
nx storybook **<your lib>**
```

This should update the `.storybook/story-loader.js` with stories in your lib.

Then just run the command to start your app, you should see the storybook for your lib.

## Troubleshooting

### **Error: Couldn’t find a navigation object**

If you are using the library `@react-navigation/native` and you are using hooks like `useNavigtion` and `useRoute` inside your component, you are likely to get the below error:

![](/blog/images/2022-04-25/1*oKNqqay19gpvIRgW1QGbkA.avif)
_Render Error for Couldn’t find a navigation object_

The easiest way is just to mock this library and create a [decorator](https://storybook.js.org/docs/react/writing-stories/decorators) for it:

> > > GO GET VIDEO FROM use-storybook-with-nx-react-native-2ddd8c010eda.html

Then in your story, you just need to add the above `NavigationDecorator`:

> > > GO GET VIDEO FROM use-storybook-with-nx-react-native-2ddd8c010eda.html

Now, this error should go away and you should see your component in your storybook.

If your component is using the `useRoute` hook and expecting certain routing parameters, then you need to customize the mock `NavigationDecorator` for your component. For example, below is a component that is expecting an id from the route parameters:

```
const route = useRoute<RouteProp<{ params: { id: string } }>>();const id = route.params?.id;
```

The mock `NavigationDecorator` will become:

> > > GO GET VIDEO FROM use-storybook-with-nx-react-native-2ddd8c010eda.html

### **Error: Could not find “store”**

If you are using Redux store and your component is stateful and connected to the store, you are likely to get the below error:

![](/blog/images/2022-04-25/1*T-Lj4PjuAlb_TbpSU5_1PQ.avif)
_Render Error for Could not find “store”_

The simple solution is to mock the store. First, you need to install the library [redux-mock-store](https://github.com/reduxjs/redux-mock-store) and its typing:

```shell
**_\# npm_**
npm install redux-mock-store @types/redux-mock-store --save-dev**_\# yarn_**
yarn add redux-mock-store @types/redux-mock-store --dev
```

Similarly, like how you mock up the navigation, you need to mock up the store. The below example mocks the store with the initial root state:

> > > GO GET VIDEO FROM use-storybook-with-nx-react-native-2ddd8c010eda.html

You can add this store decorator to your story:

### **Error: Actions must be plain objects**

If you use an async action (for example, an action created using `createAsyncThunk` from `@reduxjs/toolkit`), you would likely run into the below error: Actions must be plain objects.

![](/blog/images/2022-04-25/1*sJXG_eFpItyPt7ilyF19fw.avif)
_Render Error for Actions must be plain objects_

Now to resolve this, add thunk to mock store middleware:

> > > GO GET VIDEO FROM use-storybook-with-nx-react-native-2ddd8c010eda.html

## Conclusion

Here are how to use Storybook with Nx React Native and some common errors you may run into. With Nx React Native, you can quickly view Storybook with a toggle option in Debug Menu. It allows developers to interact and test with components during development.

Check out my previous blogs about Nx React Native:

[

### Step by Step Guide on Creating a Monorepo for React Native Apps using Nx

### Do you want to have both mobile and web apps in the same repo? Do you wish that you could share code between mobile and…

blog.nrwl.io

](https://medium.com/step-by-step-guide-on-creating-a-monorepo-for-react-native-apps-using-nx-704753b6c70e?source=post_page-----2ddd8c010eda--------------------------------)[

### Share code between React Web & React Native Mobile with Nx

### This article shows how I added a React web app and a React Native mobile app in the same monorepo using Nx

blog.nrwl.io

](https://medium.com/share-code-between-react-web-react-native-mobile-with-nx-fe5e22b5a755?source=post_page-----2ddd8c010eda--------------------------------)

### Where to go from here?

- [join the community Slack](https://go.nrwl.io/join-slack)
- [follow Nx on Twitter](https://twitter.com/nxdevtools)
- subscribe to the [Nx Youtube channel](https://youtube.com/c/Nrwl_io)
