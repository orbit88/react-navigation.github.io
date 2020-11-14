---
id: upgrading-from-5.x
title: Upgrading from 5.x
sidebar_label: Upgrading from 5.x
---

React Navigation 6 keeps the same API as React Navigation 5, however there are some breaking changes to make the API more consistent, more flexible and less confusing.

This guide lists all the breaking changes that you need to keep in mind when upgrading.

Note that it's perfectly ok to mix navigators from React Navigation 5 and React Navigation 6, so you can upgrade them separately as per your convenience.

## General changes

These following changes are in the core library. You'll need to address this changes when upgrading the `@react-navigation/native` package.

### Params are now overwrittem on navigation instead if merging

This is probably one of the biggest changes. When navigating to an existing screen, we've merged the new params with the existing params since the first version of React Navigation.

For example, let's say there's an existing `Post'` screen with the following params:

```js
{
  postTitle: 'An amazing post',
  postBody: 'Amazing content for amazing post'
}
```

And you navigate to it with `navigation.navigate('Post', { postTitle: 'An okay post' })`, it'll have the following params:

```js
{
  postTitle: 'An okay post',
  postBody: 'Amazing content for amazing post'
}
```

While this merging behavior might be useful in some scenarios, it can be problematic in other cases. We also have had many bug reports where users were confused with this behavior.

So we're changing the default behavior in React Navigation 6 so that the params aren't merged by default anymore, and new params overwrite all existing params.

While the default has changed, it's still possible to merge params if you need it. To get the previous behavior, you can pass an object to `navigate` with `merge: true` and it'll merge the params:

```js
navigation.navigate({
  name: 'Post',
  params: { postTitle: 'An okay post' },
  merge: true,
});
```

### No more `state` property on the `route` prop

The `route` prop passed to components often contained a `state` property which held state of the child navigator. While it wasn't meant to be public and we recommended against using it in the docs, we've seen a lot of people use this property.

It's problematic to use the property since it's not guaranteed to exist before navigation happens in the child navigator. This can cause subtle bugs in your app which you might not notice during development. So we have decided started warning on using this property in React Navigation 5 and removed this property entirely in React Navigation 6 prevent its usage.

If you need to have some configuration based on which screen is focused in child navigator, you can still do it using the [https://reactnavigation.org/docs/screen-options-resolution/#setting-parent-screen-options-based-on-child-navigators-state](getFocusedRouteNameFromRoute) utility.

### Linking config is now stricter

Older versions of React Navigation 5 had a slightly different configuration format for linking. The old config allowed a simple key value pair in the object regardless of nesting of navigators:

```js
const config = {
  Home: 'home',
  Feed: 'feed',
  Profile: 'profile',
  Settings: 'settings',
};
```

Let's say, your `Feed` and `Profile` screens are nested inside `Home`. Even if you don't have such a nesting with the above configuration, as long as the URL was `/home/profile`, it would work. Furthermore, it would also treat path segments and route names the same, which means that you could deep link to a screen that's not specified in the configuration. For example, if you have a `Albums` screen inside `Home`, the deep link `/home/Albums` would navigate to that screen. While that may be desirable in some cases, there's no way to prevent access to specific screens. This approach also makes it impossible to have something like a 404 screen since any route name is a valid path.

Newer versions of React Navigation 5 supported a different config format which is stricter in this regard:

- The shape of the config must match the shape of the nesting in the navigation structure
- Only screens defined in the config will be eligible for deep linking

So, you'd refactor the above config to the following format:

```js
const config = {
  screens: {
    Home: {
      path: 'home',
      screens: {
        Feed: 'feed',
        Profile: 'profile',
      },
    },
    Settings: 'settings',
  },
};
```

Here, there's a new `screens` property to the configuration object, and the `Feed` and `Profile` configs are now nested under `Home` to match the navigation structure.

While the old format still worked in React Navigation 5, React Navigation 6 drops support for the old format entirely in favor of the new strciter format.

## Stack Navigator

Thee following changes are in the `@react-navigation/stack` package.

### No more `headerMode="none"` in favor of `headerShown: false`

### Modal presentation style is now default on iOS if `mode` is set to "modal"

### Android now has a slide from bottom animation when `mode` is set to "modal"

### Props passed to header are streamlined

### Older versions of dependencies are no longer supported

## Bottom Tab Navigator

The following changes are in the `@react-navigation/bottom-tabs` package.

### The `tabBarOptions` prop is now more flexible by moving to `options`

### The `tabBarVisible` option is no longer supported

### The `lazy` prop is moved to `lazy` option for per-screen configuration

### Older versions of dependencies are no longer supported

## Material Top Tab Navigator

The following changes are in the `@react-navigation/material-top-tabs` package.

### The `tabBarOptions` prop is now more flexible by moving to `options`

### The `lazy` prop is moved to `lazy` option for per-screen configuration

## Material Bottom Tab Navigator

The following changes are in the `@react-navigation/material-bottom-tabs` package.

### The `tabBarOptions` prop is now more flexible by moving to `options`

### The `lazy` prop is moved to `lazy` option for per-screen configuration

## Drawer Navigator

The following changes are in the `@react-navigation/drawer` package.

## Show a header by default

### Slide animation (`drawerType="slide"`) is now default on iOS

### Drawer no longer emits `drawerOpen` and `drawerClose` events

### The `drawerContentOptions` prop is now more flexible by moving to `options`

### The `lazy` prop is moved to `lazy` option for per-screen configuration

### Older versions of dependencies are no longer supported
