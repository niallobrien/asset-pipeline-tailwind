# Asset Pipeline: Tailwind
Asset Pipeline: Tailwind is intended to be a drop-in asset pipeline for traditional web apps (which uses serverside templates/views). It can be used with most fullstack web frameworks.
This projects utilises ES2015, jQuery for clientside JavaScript and TailwindCSS for styles.

## Installation
If starting a new project,
`mkdir my-new-project && cd my-new-project`
`git clone https://github.com/niallobrien/asset-pipeline-tailwind .` and remove the git foler with `rm -rf .git` as you probably don't want that.
It might best not to clone this repo into an existing project. Instead, I suggest that you simply download the zip file and extract it into your project or clone the repo into another location
and move the files over.

## Required Middleware

### Pug
If you're using Pug (formally Jade) with Express, you'll need to add the below middleware
```
// Middleware for Jade/Pug custom filter for use with Laravel Mix
app.use((req, res, next) => {
  app.locals.filters = {
    'mix': (text, options) => {
      if (!text) return
      text = text.replace(/["']/g, '')

      const manifest = require(__dirname + '/public/mix-manifest.json')
      if (options.css) return `<link rel="stylesheet" href="${manifest[text]}">`
      if (options.js) return `<script type="text/javascript" src="${manifest[text]}"></script>`
    }
  }
  next()
})
```

Then add `:mix(css) '/styles/app.css'` within the `head` section of your layout file and at the bottom of the `body` section add:
```
:mix(js) '/scripts/manifest.js'
:mix(js) '/scripts/vendor.js'
:mix(js) '/scripts/app.js'
```

### Edge
If you're using Edge with AdonisJS, in `start/hooks.js` (create it if it doesn't exist) add:
```
const {hooks} = require('@adonisjs/ignitor')
const path = require('path')

hooks.after.providersBooted(() => {
  const View = use('Adonis/Src/View')
  const Helpers = use('Helpers')

  View.global('mix', text => {
    if (!text) return
    const manifest = require(path.join(Helpers.publicPath(), 'mix-manifest.json'))
    return manifest[text]
  })
})
```

Then add `<link rel="stylesheet" href="{{ mix('/assets/styles/app.css') }}">` within the `head` section of your layout file and at the bottom of the `body` section add:
```
{{-- Scripts --}}
<script src="{{ mix('/assets/scripts/manifest.js') }}"></script>
<script src="{{ mix('/assets/scripts/vendor.js') }}"></script>
<script src="{{ mix('/assets/scripts/app.js') }}"></script>
```

Please feel free to contribute examples for use with other web frameworks.

### Usage
You can run the following commands to utilise the asset pipeline.

`npm run dev` will build the assets for the development environment.

`npm run watch` will build the assets for the development environment and continue to watch the assets for further changes. When a change is detected, it will automatically rebuild the assets.

`npm run production` will build the assets for the production environment. This will minify all assets and utilise filenames that facilitate cache-busting, ideal for a production environment.
Unfortunately, PurgeCSS doesn't yet support Pug. See https://github.com/FullHuman/purgecss/issues/6

*Note:* When adding to an existing project, make sure to copy the `scripts`, `dependencies` and `devDependencies` from `package.json` to your exisiting `package.json` file.