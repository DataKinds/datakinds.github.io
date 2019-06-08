---
layout: post
title: 'The Spirit of Play: Ride Along Game Development in Haskell'
date: 2019-06-05 21:23 -0700
---
I got into programming when I was 8 because I wanted to make games. I guess, deep down, I was never truly able to satiate that desire. I've never released a game, I've never completed a game, and hell -- I've never gotten far enough to even draw up assets for a game.

But I ran into a library yesterday called Raylib that kind of rekindled the playful fire that lived deep down under my pragmaticism. Raylib lives at [https://www.raylib.com/](https://www.raylib.com/). I immediately fired up VS Code and hammered out some C for the first time in two or three years. Suffice to say, I had _fun_. Not fun like "wow, this code is elegant," or fun like "wow I'm making a lot of money doing this," but fun like "I'm giggling at the screen because I made a circle bounce back and forth."

And that was an incredible feeling.

So, in the spirit of all things good: if it's worth doing, it's worth overdoing. That's why I'm writing this blog post. 

This won't be like any other blog post that I've written. I am writing this post live as I'm experimenting with building a game from the bottom up in Haskell. You'll see every single decision I make, and everything I find exciting enough to share, even if I go back on it five minutes later. No game frameworks, no library bindings -- just me, Stack, a fresh install of [GLFW](https://www.glfw.org/), and a readiness to get hacking. 

In one window, I have my blog post. In the other window, my code. Without further ado...

## 9:23 PM, day 1

I came up with the idea and started writing this blog post.

## 11:19 PM

Finally got around to initializing the stack project!  

![Stack project screenshot](/assets/imgs/ride-along-game-dev/1119.png)

## 12:57 AM

Here's my first attempt at defining enough of a wrapper around GL and GLFW in order to [run the example code on the GLFW documentation page](https://www.glfw.org/documentation.html). This is before I've even tried compiling it, so this is pre-iteration-1:

`GLFW.hs`:

```hs
{-# LANGUAGE ForeignFunctionInterface #-}

module GLFW where

import Control.Monad

-- | Init functions
foreign import ccall "glfwInit" init :: IO Int
foreign import ccall "glfwTerminate" terminate :: IO ()

-- | Window functions
data Window = InternalWindow deriving (Storable)
foreign import ccall "glfwCreateWindow" createWindow :: Int -> Int -> CString -> IO (Ptr Window)
foreign import ccall "glfwWindowShouldClose" _windowShouldClose :: Ptr Window -> IO Int
windowShouldClose :: Ptr Window -> IO Bool
windowShouldClose = fmap (0 /=) . _windowShouldClose
foreign import ccall "glfwSwapBuffers" swapBuffers :: Ptr Window -> IO ()
foreign import ccall "glfwPollEvents" pollEvents :: IO ()

-- | Context functions
foreign import ccall "glfwMakeContextCurrent" makeContextCurrent :: Ptr Window -> IO ()
```

`GL.hs`:

```hs
{-# LANGUAGE CPP, ForeignFunctionInterface #-}

module GL where

#include <GL/gl.h>

data ClearMask = COLOR_BUFFER_BIT | DEPTH_BUFFER_BIT | STENCIL_BUFFER_BIT
foreign import ccall "glClear" _clear :: Int -> IO ()
clear :: ClearMask -> IO ()
clear COLOR_BUFFER_BIT = _clear GL_COLOR_BUFFER_BIT
clear DEPTH_BUFFER_BIT = _clear GL_DEPTH_BUFFER_BIT
clear STENCIL_BUFFER_BIT = _clear GL_STENCIL_BUFFER_BIT
```

## 1:08 AM

Turns out including header files in Haskell isn't as easy as using `#include <GL/gl.h>`.

![C preprocessor error](/assets/imgs/ride-along-game-dev/108.png)

## 6:38 PM, day 3

After a day long break where I was busy with some freelance work, I came back to this project and finally made some progress! After fixing some linker errors a couple Haskell bugs...

We have a window! 

![Hello, window!](/assets/imgs/ride-along-game-dev/638.png)

### Interlude: Why Haskell?

> I believethat the monadic approach to programming, in which actions are first class values, is itself interesting, beautiful, and modular. In short, Haskell is the world’s finest imperative programming language.

[from https://www.microsoft.com/en-us/research/wp-content/uploads/2016/07/mark.pdf](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/07/mark.pdf).