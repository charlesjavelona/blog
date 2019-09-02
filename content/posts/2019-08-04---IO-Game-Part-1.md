---
title: "Building an IO Game"
date: "2019-08-04T22:12:03.284Z"
template: "post"
draft: true
slug: "/posts/io-game-part-1/"
category: "Game"
tags:
  - "Game"
  - "IO"
description: "Step by step process of creating an IO game"
---
#### Game Summary:
The game is simple, you control a ship that fires bullets. A shield can be activated to protect yourself from the bullets. There will be other players that can join the game. The more players you kill, your score increases.

#### Table of Contents:
- 1.) Project Overview - A high level overview of the project, including a folder structure.
- 2.) Project Setup - Important setup and configurations. 
- 3.) Client Entry Points - `index.html` and `index.js`.
- 4.) Client Networking - Communicating with the server.
- 5.) Client Rendering - Assets such as images, css, etc.. and displaying the game.
- 6.) Client Input - Commands and other inputs on how users will play the game.
- 7.) Client State - Processing updates from client to server.

Next step is the server [insert next post here].

#### 1.) Project Structure
The following tech stack will be used. 
  - [Express](https://expressjs.com/), a Javascript web server library. 
  - [Webpack](https://webpack.js.org/), a automated module bundler.
  - [Socket.io](https://socket.io/), a real-time communication library between client and server.

```js
public/
    assets/
      ...
src/
    client/
      css/
      ...
      html/
        index.html
      index.js
        ...
    server/
      server.js
      ...
    shared/
      ...
```

`public/`
The `public/` folder will be static files served by the server.

`src/`
The `src/` folders contain the `client` and `server` files which syncs up together to create the game.
The `shared/` folder are code that can be re-used by the client and server.

#### 2.) Project Setup:

```js
// header: webpack.common.js
const path = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin')


module.exports = {
  entry: {
    game: './src/client/index.js',
  },
  output: {
    filename: '[name].[contenthash].js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ['@babel/preset-env'],
          },
        },
      },
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
          },
          'css-loader',
        ],
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name].[contenthash].css',
    }),
    new HtmlWebpackPlugin({
      filename: 'index.html',
      template: 'src/client/html/index.html',
    }),
  ],
};
```
Is where the starting point of the code execution when webpack is run by node.
```js
  entry: { 
    game: './src/client/index.js'
  } 
```

After webpack finishes executing. All the code is bundled up to different optimized files. The filenames have a hash after the name of a file `example.[hashed].js`
```js
output: { 
  filename: '[name].[contenthash].js', 
  path: path.resolve(__dirname 'dist')
},
```

Every file bundled from webpack is placed into a `dist/` folder. This folder is a static folder which is served to the client side.

```js
module: { 
  rules:[
    ...
  ],
  ...
}
```
The modules section is where the rules are applied when files are being passed through the webpack execution code during runtime. I won't go deep into this as there are many rules that can be applied to webpack. 

```js
 plugins: [
   ...
 ]
```
THe plugins sections are where webpack enables third party libraries to add value in your code. Again, I won't go in depth with them because there are a lot of plugins in webpack

The `webpack.commons.js` is where common configurations are held for the front-end. Development and production configurations are held in separate files and are merge by `webpack merge`.

```js
// header: webpack.dev.js
const merge = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'development',
});
```


#### 3.) Client Entry:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Multiplayer shooting game</title>
  <link type="text/css" rel="stylesheet" href="/game.bundle.css">
</head>
<body>
  <canvas id="game-canvas"></canvas>
  <script async src="/game.bundle.js"></script>
  <div id="play-menu" class="hidden">
    <input type="text" id="username-input" placeholder="Username" />
    <button id="play-button">PLAY</button>
  </div>
</body>
</html
```
There are three item in this section that is important to understand.
  
  1.) `<canvas id="game-canvas" .../>`, is where the game will be rendered.

  2.) The `<link .../>` tag will is where CSS is referred to for styling.

  3.) The `<script .../>` tag is where the Javascript files is referred to when manipulating the games logic.

  4.) `<input .../>`, is an input bar which asks for the players name.

When the home page is loaded in the browser. The Javascript code will execute starting at the entry point `src/client/index.js`.

```js
// header: index.js
import { connectGame, playGame } from './clientServerConnection';
import { startRendering, stopRendering } from './render';
import { startCapturingInput, stopCapturingInput } from './input';
import { downloadAssets } from './assets';
import { initState } from './state';
import { setLeaderboardHidden } from './leaderboard';

import './css/main.css';

const playMenu = document.getElementById('play-menu');
const playButton = document.getElementById('play-button');
const usernameInput = document.getElementById('username-input');

Promise.all([
  connectGame(),
  downloadAssets(),
]).then(() => {
  playMenu.classList.remove('hidden');
  usernameInput.focus();
  playButton.onclick = () => {
    // Play!
    playGame(usernameInput.value);
    playMenu.classList.add('hidden');
    initState();
    startCapturingInput();
    startRendering();
    setLeaderboardHidden(false);
  };
});
```

Here's what's happening in the above file:

1.) At the top of the file, import all js and css files.

2.) Run `connectGame()` to establish the connection from client to server. Also, run `downloadAssets()` to download all the required assets such as images into the game.

3.) `playMenu.classList.remove('hidden');` overrides the CSS to show the user input.

4.) Once the PLAY button is clicked initialized all the functions that tells the server to start the game.

#### 4.) Client Networking:

To communicate back and forth between the server and client using the same connection, [server.io](https://server.io) will be the tool to use. It uses the [websockets protocol](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) to provide two way communication without [polling](https://en.wikipedia.org/wiki/Polling_(computer_science)) the server to reply. In short, websockets provide real-time communication between the server and client.

There will be one file that will be responsible for handling all the connection between the client and server. `src/client/clientServerConnection.js`

```js
import io from 'socket.io-client';
import { processGameUpdate } from './state';

const Enums = require('../shared/enums');

const socket = io(`ws://${window.location.host}`);
const connectedPromise = new Promise(resolve => {
  socket.on('connect', () => {
    console.log('Connected to server!');
    resolve();
  });
});

export const connectGame = onGameOver => (
  connectedPromise.then(() => {
    // Callbacks registered
    socket.on(Enums.MSG_TYPES.GAME_UPDATE, processGameUpdate);
    socket.on(Enums.MSG_TYPES.GAME_OVER, onGameOver);
  })
);

export const playGame = username => {
  socket.emit(Enums.MSG_TYPES.JOIN_GAME, username);
};

export const updateDirection = dir => {
  socket.emit(Enums.MSG_TYPES.INPUT, dir);
};

```
Here's what's happening in the above file.

1.) Import all the necessary Javascript files.

2.) Grab the location of the backend localhost, the url is `ws://${window.location.host}`.

3.) Use the function `connectedPromise()` inside the function `connectGame()` to initialized the connection to the backend. The function, `connectedPromise()` resolves only when the backend connects.

4.) If the connection succeeds, register two callbacks, `processGameUpdate()` and `onGameOver` waiting for messages received from the server.

#### 5.) Client Rendering:

Before displaying things on screen. Images needed to be dowloaded. A file called `assets.js` manages all the tasks for this.

```js
const ASSET_NAMES = ['ship.svg', 'bullet.svg'];
const assets = {};
const downloadPromise = Promise.all(ASSET_NAMES.map(downloadAsset));

function downloadAsset(assetName) {
  return new Promise(resolve => {
    const asset = new Image();
    asset.onload = () => {
      console.log(`Downloaded ${assetName}`);
      assets[assetName] = asset;
      resolve();
    };
    asset.src = `/assets/${assetName}`;
  });
}

export const downloadAssets = () => downloadPromise;
export const getAsset = assetName => assets[assetName];
```
