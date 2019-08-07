---
title: "Building an IO Game"
date: "2019-08-04T22:12:03.284Z"
template: "post"
draft: false
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

***Webpack Setup: webpack.common.js***
```js
const path = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

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