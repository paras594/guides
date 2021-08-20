# Gatsby JS Guide



### Initial Setup

```bash
# install gatsby
> npm i -g gatsby-cli

# create project
> gatsby new <project-name> https://github.com/gatsbyjs/gatsby-starter-hello-world

# start project
> gatsby develop

# create production build
> gatsby build

# serve production build locally
> gatsby serve
```



### Styled components with Gatsby

```bash
# install 
> npm install gatsby-plugin-styled-components styled-components babel-plugin-styled-components

# add plugin to gatsby config
module.exports = {
  plugins: [`gatsby-plugin-styled-components`]
}
```







