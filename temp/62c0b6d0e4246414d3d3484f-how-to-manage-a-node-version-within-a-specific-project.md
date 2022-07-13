## How to manage a node version within a specific project? 

 If you have landed here chances are you already know about the node and want to figure out how you can use different versions across your projects. If you don't know [what is node](https://nodejs.org/en/), you can checkout another short article where I talk about it.

> [What the heck is Node.js?](https://devpub.hashnode.dev/what-the-heck-is-node-js-12ae5639d22f).

Here I will show a way to use a specific version of node within your project. A prerequisite for this is to install nvm package on your system that is used to manage multiple node versions.


## Step - 1

Run ```
node -v > .nvmrc
```  on terminal
This will create a  ```
.nvmrc
```  file in the project directory. This file contains the node version to be used.  


![Screenshot 2022-07-03 at 1.11.29 AM.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1656796204436/5JBmH02IX.png align="left")

Now, whenever we run  ```
nvm use
```  in our project directory it will pick the node version listed in the  ```
.nvmrc
```  file. But we don’t want to run this command every time. Instead, we want **nvm** to automatically pick the node version whenever we are in this directory. 

For that, we have to make changes to our terminal’s configuration file. 
I will cover **zsh** &  **bash**.


## Step - 2

### For ZSH
In the root directory of your system. There would be a ```
.zprofile
```  or  ```
.zshrc
```  file. This would be a hidden file, you can run ```
ls -a
```  on the command line or ```
command + shift + .
``` on your Mac system. If there is not any you can create one.

There all you have to do is put this piece of code to let ZSH search for the .nvmrc file in the directory to pick the desired node version.

```
# place this after nvm initialization!
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc
```

You can follow [this link](https://github.com/nvm-sh/nvm#zsh) to get the code.


### For BASH
In the root directory of your system. There would be a ```
.bash_profile
```  or  ```
.bashrc
```  file. Again, this would be a hidden file, you can run  ```
ls -a
``` on the command line or ```
command + shift + .
``` on your Mac system. If there is not any you can create one.

There all you have to do is put this piece of code to let BASH search for the ```
.nvmrc
``` file in the directory to pick the desired node version. 

You can follow [this link](https://github.com/nvm-sh/nvm#bash) to get the code. 

Relevant stack overflow [answer](https://stackoverflow.com/questions/57110542/how-to-write-a-nvmrc-file-which-automatically-change-node-version)


**Here is how it behaves
**

![abcd.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1656796580593/tt-Tu9el2.gif align="left")


## Alternate Solution
As an alternative to the NVM, we can also use nodeenv to manage node versions in our project. 
Following is the link which shows how to do that.
https://stackoverflow.com/a/61356070

I hope you liked the content, if you have any suggestions for improvements do let me know! 

