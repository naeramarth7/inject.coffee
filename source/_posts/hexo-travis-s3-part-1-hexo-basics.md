title: 'Hexo, Travis, S3 - Part 1: Hexo basics'
tags:
  - Hexo
date: 2015-07-29 20:35:48
---


As stated in the {% post_link hexo-with-travis-ci-on-amazon-s3-introduction Introduction %}, Hexo is build on [Node.js](https://nodejs.org/). So, if not already done, install it! I recommend using [nvm](https://github.com/creationix/nvm), the **n**ode **v**ersion **m**anager.

<!-- more -->

## Installing dependencies: nvm, node and npm

On OS X using brew, this is the [preferred way](https://stackoverflow.com/questions/28017374/what-is-the-suggested-way-to-install-brew-node-js-io-js-nvm-npm-on-os-x):

```bash
brew update
brew install nvm
# depending on your environment, change use .profile, .bashrc or .zshrc
echo "source $(brew --prefix nvm)/nvm.sh" >> ~/.profile
source ~/.profile
nvm install 0.12
nvm default 0.12
```

Otherwise install `nvm` using the install script provided on github:

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.25.4/install.sh | bash
nvm install 0.12
nvm default 0.12
```

# Setting up a new hexo website

Now that we have `node` and `npm` (which ships with `node`), we can install `hexo` and create our first website with just a few commands:

```bash
# Install hexo globally
npm install -g hexo

# Initialize a new Hexo website
hexo init my-website
cd my-website

# Install the dependencies
npm install

# Start a local webserver serving your new hexo website
hexo server
```

That's it! Open [localhost:4000](http://localhost:4000/) in your browser to see Hexo in action - using the Hexo 3 default theme [landscape](https://github.com/hexojs/hexo-theme-landscape).

## Version control your website

Now that you got everything up and running, don't forget to set up a repository and version control your new website. The Hexo generator already provides a `.gitignore` file for the basic assets and logs.

```bash
git init
git add .
git commit -m 'initial commit'
```

## File and folders

Let's take a look at Hexo's file structure:

```bash
drwxr-xr-x  7 root root  4096 Jun 22 14:17 .
drwxr-xr-x  3 root root  4096 Jun 22 14:12 ..
drwxr-xr-x  8 root root  4096 Jun 22 14:18 .git
-rw-r--r--  1 root root    65 Jun 22 14:12 .gitignore
-rw-r--r--  1 root root  1477 Jun 22 14:12 _config.yml
-rw-r--r--  1 root root 14536 Jun 22 14:13 db.json
drwxr-xr-x 12 root root  4096 Jun 22 14:12 node_modules
-rw-r--r--  1 root root   447 Jun 22 14:13 package.json
drwxr-xr-x  2 root root  4096 Jun 22 14:12 scaffolds
drwxr-xr-x  3 root root  4096 Jun 22 14:12 source
drwxr-xr-x  3 root root  4096 Jun 22 14:12 themes
```

|               |                                                                                                                    |
|---------------|--------------------------------------------------------------------------------------------------------------------|
| `_config.yml` | The settings for your website. Take a look at the [docs](https://hexo.io/docs/configuration.html) for some basics. |
| `db.json`     | Stores your data when using `hexo server` to preview your website                                                  |
| `scaffolds`   | Templates for page layouts like `page`, `post` and `draft`                                                         |
| `source`      | Pages and posts. Your static content that will be rendered.                                                        |
| `themes`      | Well, the installed themes, obviously.                                                                             |

Pretty simple. Right?

## Customizing Hexo

Due to Hexo's modularity, it's pretty simple to install new [plugins](https://hexo.io/docs/plugins.html) and [themes](https://hexo.io/docs/themes.html). All you need is just a single `npm install` or `git submodule add` away.

### Installing a new theme

There are multiple ways of installing an existing theme for your Hexo website. The examples use [LouisBarranqueiro](https://github.com/LouisBarranqueiro)'s theme [tranquilpeak](https://github.com/LouisBarranqueiro/tranquilpeak-hexo-theme themes/tranquilpeak) - the same [theme this blog](https://github.com/naeramarth7/tranquilpeak-hexo-theme) uses, with some slight modifications.

#### Option 1: Install as git submodule

```bash
git submodule add https://github.com/LouisBarranqueiro/tranquilpeak-hexo-theme themes/tranquilpeak
cd themes/tranquilpeak

# install bower and grunt if not already installed
npm install -g bower grunt-cli

npm install
bower install
grunt build
```

#### Option 2: Install as part of the repository

```bash
cd themes
curl -L https://github.com/LouisBarranqueiro/tranquilpeak-hexo-theme/releases/download/v1.2.0/tranquilpeak-hexo-theme-built-for-production-1.2.0.zip > tranquilpeak.zip
unzip tranquilpeak.zip
rm tranquilpeak.zip
mv tranquilpeak-hexo-theme-built-for-production-1.2.0 tranquilpeak
```

#### Enabling a theme

After the installation, change the `theme` variable inside `_config.yml` to your themes (folder) name

```yaml
theme: tranquilpeak
```
**Note:** When making changes to the `_config.yml` file, you need to cancel at start the `hexo server` over, since those changes are not detected on run time.

For further configuration and customization, take a look at Hexo's `_config.yml` as well as the one the theme of your choice provides.

#### Conclusion

As you can see, setting up and running Hexo is done within minutes - and it attempts to stay as simple and minimalistic as possible. This is where Hexo - in my opinion - succeeded other static site generators I tried (like Jekyll, Metalsmith and Harp).

In the next part of this series we're going to set up an Amazon S3 bucket to deploy our previously created Hexo website into, and make it available to the world using the AWS stack.
