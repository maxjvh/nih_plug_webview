# nih-plug-webview

**Warning: work in progress, not production-ready yet.**
Experimental webview editor for [nih-plug](https://github.com/robbert-vdh/nih-plug) using [wry](https://github.com/tauri-apps/wry).
Built on top of [baseview](https://github.com/RustAudio/baseview).

## Compatability

Currently, Windows and macOS are supported and have been tested.

## Issues

On macOS there is an unresolved issue where pressing the escape key in Ableton Live will lead to a crash.
I've reported this to Ableton, and I'm currently mitigating this by consuming the escape keypress behind the scenes.

## Features
- Send arbitrary JSON values back and forth to the webview using Serde
- Resizable plug-in window
- Drag and drop files with full paths
- Callback for deciding which key events from DAW to consume 
- Customisable background color for when the view is still loading (avoid initial flash of white)
- Use devtools

## Usage

### Web Framework (Recommended)
**Note**: I'll provide basic examples using Next.js, since it's the framework I'm most familiar with. If you're not sure how to do something, always check your framework's documentation! (RTFM)
#### Prerequisites
- An [nih-plug](https://github.com/robbert-vdh/nih-plug) project
- A GUI directory for your web framework
    (read your framework's documentation on creating a project; For example, Next.js uses `npx create-next-app@latest`)

#### Compatability
Generally, if your web framework can output to static content (most popular frameworks can), you'll be fine. 

#### Setup
First, configure your framework to output static content. Here's an example for Next.js. 

```mjs
// next.config.mjs

/** @type {import('next').NextConfig} */
const nextConfig = {
    output: 'export',
};

export default nextConfig;
```

In NIH-plug's `editor` function (in `impl Plugin`), you might want something like:

```rust
// with debug builds, refer to localhost
#[cfg(debug_assertions)]
let src = HTMLSource::URL("http://localhost:3000");
// for release builds, use static content
#[cfg(not(debug_assertions))]
let src = HTMLSource::String(include_str!("<path/to/index.html>"));
let editor = WebViewEditor::new(src, (300, 300)).with_frontend_dir("<base/static/export/path>".into())
// ... other editor configuration
```
**NOTE**: Make sure to replace the paths in the example!

With a setup like this, you can develop your GUI on localhost (make sure to start the dev server!), and then export the app to static files and use those in release versions of your plugin. (Because user machines don't have access to a dev server..)

### Plain HTML/CSS/JS
Similarly to using a web framework, you should probably have an existing NIH-plug project set up before starting with this. 

If you want to write your GUI in HTML, CSS, and JS, there are a few options.

One option is to write and inline everything (*all* CSS/JS scripts) in one HTML file and reference it with `HTMLSource::String`. Check out [the example.](https://github.com/maxjvh/nih-plug-webview/blob/main/example/src/)

If you don't want to do this and want separate files, you'll need `.with_frontend_dir` so that your HTML file can correctly find the other files.