# react-scripts

This package includes scripts and configuration used by [Create React App](https://github.com/facebookincubator/create-react-app).<br>

To customize, create `config/webpack.custom.{dev|prod}.js` and export a function. The function will be given the react-scripts webpack configuration as the only parameters and
should return a webpack configuration object (modified as you want).

An example custom dev config file:
```
const cssModulesDev = '?modules&camelCase&importLoaders=1&localIdentName=';

module.exports = (c) => {
   try {
   c.module.rules[1].exclude.push(/\.(scss|sass)/)
   c.module.rules[3].options.presets.push('babel-preset-stage-0')
   c.module.rules[3].options.plugins = ["transform-decorators-legacy"]
   c.module.rules.push(
      {
         test: /(\.scss|\.sass)$/,
         use: [
            {
               loader: "style-loader"
            },
            {
               loader: "css-loader",
               options: {
                  modules: true,
                  localIdentName: '[name]__[local]___[hash:base64:5]'
               }
            },
            {
               loader: "sass-loader"
            }
         ]
      }
   )
   } catch (e) {
      console.error(e)
      process.exit(1)
   }
   return c
}
```
This adds `stage-0`, `@decorators` and `sass` with `css-modules`
