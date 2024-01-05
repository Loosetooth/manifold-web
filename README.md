## About manifold-web

This is a package made with the goal of having a Webpack compatible npm module for [manifold](https://github.com/elalish/manifold), for use in the browser. (Not node.js)
See [the discussion](https://github.com/elalish/manifold/discussions/372) for more details.

## Installation and use

To install:
`npm install git+https://github.com/Loosetooth/manifold-web.git`

Please check out the different branches for each version of manifold.
In order to install a specific branch:

`npm install git+https://github.com/Loosetooth/manifold-web.git#v2.2.2`

Some tips:

You might need to pass a reference of the `.wasm` url to the manifold instance:
```js
import Module from "manifold-web";
import manifold_wasm from 'manifold-web/manifold.wasm?url'

const wasm = await Module({
    locateFile: () => manifold_wasm,
});
wasm.setup()
```

You might also need to add this to your webpack config to pack .wasm files:
```js
config.module.rules.push({
    test: /\.wasm$/,
    type: "javascript/auto",
    loader: "file-loader",
    options: {
    name: "static/js/[name].[contenthash:8].[ext]",
    },
})
```

Or for vue.js:
```js
module.exports = {
  chainWebpack: (config) => {
    config.module
      .rule('wasm')
      .test(/\.wasm$/)
      .use('file-loader')
      .loader('file-loader')
      .tap((options) => {
        return {
          name: "static/js/[name].[contenthash:8].[ext]",
        }
      })
  },
}
```

## Steps to build:
The steps to create this package are exactly the same as [here](https://github.com/polygonjs/polygonjs/tree/integration-manifold/src/core/geometry/sdf/manifold) but are mentioned again in detail here:

### Preparation

You'll need node.js installed.
Then run `npm install`

### Create manifold WASM build

Clone [the manifold repo](https://github.com/elalish/manifold) and do the WASM build.
For details on how to do this, see [the manifold readme](https://github.com/elalish/manifold#wasm)
After building, the files we're interested in can be found in `manifold/buildWASM/bindings/wasm`

Copy the following files into this repo:
```
"files": [
    "manifold.js",
    "manifold.wasm",
    "manifold.d.ts",
    "manifold-encapsulated-types.d.ts",
    "manifold-global-types.d.ts",
    "editor.d.ts",
    "examples.js"
]
```

### Modify manifold.js

Since manifold.js is minified, we'll format it in preparation for the changes that have to be made:
```sh
npx prettier --write .
```

Manually modify the following content in `manifold.js` to be compatible with webpack:
* remove the content of the `if (ENVIRONMENT_IS_NODE) {}` block
* comment out the line `wasmBinaryFile = new URL('manifold.wasm', import.meta.url).href;`



Original readme left here for reference:


## [ManifoldCAD.org](https://manifoldcad.org)
If you like OpenSCAD / OpenJSCAD, you might also like ManifoldCAD - our own solid modelling web app based on this package. 

![A metallic Menger sponge](https://elalish.github.io/manifold/samples/models/mengerSponge3.webp "A metallic Menger sponge")

# Manifold

[**High-level Documentation**](https://elalish.blogspot.com/search/label/Manifold) | [**API Documentation**](https://elalish.github.io/manifold/docs/html/modules.html) | [**Algorithm Documentation**](https://github.com/elalish/manifold/wiki/Manifold-Library) | [**Web Examples**](https://elalish.github.io/manifold/model-viewer.html)

[Manifold](https://github.com/elalish/manifold) is a geometry library dedicated to creating and operating on manifold triangle meshes. A [manifold mesh](https://github.com/elalish/manifold/wiki/Manifold-Library#manifoldness) is a mesh that represents a solid object, and so is very important in manufacturing, CAD, structural analysis, etc. Further information can be found on the [wiki](https://github.com/elalish/manifold/wiki/Manifold-Library).

## What's here

This library is fast with guaranteed manifold output. As such you need manifold meshes as input, which this library can create using constructors inspired by the OpenSCAD API, as well as more advanced features like smoothing and signed-distance function (SDF) level sets. You can also pass in your own mesh data, but you'll get an error status if the imported mesh isn't manifold. Various automated repair tools exist online for fixing non manifold models, usually for 3D printing. 

The most significant contribution here is a guaranteed-manifold [mesh Boolean](https://github.com/elalish/manifold/wiki/Manifold-Library#mesh-boolean) algorithm, which I believe is the first of its kind. If you know of another, please open a discussion - a mesh Boolean algorithm robust to edge cases has been an open problem for many years. Likewise, if the Boolean here ever fails you, please submit an issue! This Boolean forms the basis of a CAD kernel, as it allows simple shapes to be combined into more complex ones.

## Note on memory management

Since Manifold is a WASM module, it does not automatically garbage-collect like regular JavaScript. You must manually `delete()` each manifold object constructed by your scripts, see https://github.com/elalish/manifold/discussions/256#discussioncomment-3944287.

## About the author

This library was started by [Emmett Lalish](https://elalish.blogspot.com/). I am currently a Google employee and this is my 20% project, not an official Google project. At my day job I'm the maintainer of [\<model-viewer\>](https://modelviewer.dev/). I was the first employee at a 3D video startup, [Omnivor](https://www.omnivor.io/), and before that I worked on 3D printing at Microsoft, including [3D Builder](https://www.microsoft.com/en-us/p/3d-builder/9wzdncrfj3t6?activetab=pivot%3Aoverviewtab). Originally an aerospace engineer, I started at a small DARPA contractor doing seedling projects, one of which became [Sea Hunter](https://en.wikipedia.org/wiki/Sea_Hunter). I earned my doctorate from the University of Washington in control theory and published some [papers](https://www.researchgate.net/scientific-contributions/75011026_Emmett_Lalish).
