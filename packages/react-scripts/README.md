# react-scripts

This package includes scripts and configuration used by [Create React App](https://github.com/facebookincubator/create-react-app).<br>

To customize, create `config/webpack.custom.{dev|prod}.js` and export a function. The function will be given the react-scripts webpack configuration as the only parameters and
should return a webpack configuration object (modified as you want).

An example custom dev config file:
```
const log = require('tmpljs/log')                                                                                     
const autoprefixer = require('autoprefixer')                                                                          
const {addFileLoaderExclude, addBabelPreset, addBabelPlugins, addRule} = require('./config-helpers')                  
                                                                                                                      
module.exports = (c) => {                                                                                             
   try {                                                                                                              
      addFileLoaderExclude(c, /\.(scss|sass)/)                                                                        
      addBabelPreset(c, 'babel-preset-stage-0')                                                                       
      addBabelPlugins(c, 'transform-decorators-legacy')                                                               
      addRule(c, {                                                                                                    
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
            },                                                                                                        
            {                                                                                                         
               loader: require.resolve('postcss-loader'),                                                             
               options: {                                                                                             
                  ident: 'postcss', // https://webpack.js.org/guides/migrating/#complex-options                       
                  syntax: 'postcss-scss',                                                                             
                  plugins: () => [                                                                                    
                     require('postcss-flexbugs-fixes'),                                                               
                     autoprefixer({                                                                                   
                        browsers: [                                                                                   
                           '>1%',                                                                                     
                           'last 4 versions',                                                                         
                           'Firefox ESR',                                                                             
                           'not ie < 9', // React doesn't support IE8 anyway                                          
                        ],                                                                                            
                        flexbox: 'no-2009',                                                                           
                     }),                                                                                              
                  ]                                                                                                   
               },                                                                                                     
            },                                                                                                        
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
./config-helpers
```
const merge = require('lodash/merge')

const getRule = (c, predicate) => {
   const {rules} = c.module
   const rule = rules.filter(r => predicate(r))
   if (rule.length != 1) {
      return
   } else {
      return rule[0]
   }
}

const loaderMatch = (loader) => {
   return r => r.loader === require.resolve(loader)
}

const getLoaderRule = (c, loader) => {
   return getRule(c, loaderMatch(loader))
}

const getFileLoaderRule = (c) => {
   return getLoaderRule(c, 'file-loader')
}

const addFileLoaderExclude = (c, ...exclude) => {
   const rule = getFileLoaderRule(c)
   if (rule) {
      if (rule.exclude) {
         rule.exclude = [...rule.exclude, ...exclude]
      } else {
         rule.exclude = exclude
      }
   } else {
      throw new Error('Unable to add excludes, file loader rule not found')
   }
}


const getBabelLoaderRule = (c) => {
   return getLoaderRule(c, 'babel-loader')
}

const addBabelPreset = (c, ...preset) => {
   const rule = getBabelLoaderRule(c)
   if (rule) {
      const newRule = merge({options: {presets: []}}, rule)
      newRule.options.presets = [...newRule.options.presets, ...preset]
      Object.assign(rule, newRule)
   } else {
      throw new Error('Unable to add Babel Loader presets, rule not found')
   }
}

const addBabelPlugins = (c, ...plugins) => {
   const rule = getBabelLoaderRule(c)
   if (rule) {
      const newRule = merge({options: {plugins: []}}, rule)
      newRule.options.plugins = [...newRule.options.plugins, ...plugins]
      Object.assign(rule, newRule)
   } else {
      throw new Error('Unable to add Babel Loader plugins, rule not found')
   }
}

const addRule = (c, rule) => {
   const {rules} = c.module
   rules.push(rule)
}

module.exports = {
   getRule,
   loaderMatch,
   getLoaderRule,
   getFileLoaderRule,
   addFileLoaderExclude,
   getBabelLoaderRule,
   addBabelPreset,
   addBabelPlugins,
   addRule,
}
```
This adds `stage-0`, `@decorators` and `sass` with `css-modules`
