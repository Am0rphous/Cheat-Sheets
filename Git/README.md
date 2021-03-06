# Git

- [Gthub - Basic Writing and Formatting Syntax](https://docs.github.com/en/enterprise-server@2.20/github/writing-on-github/basic-writing-and-formatting-syntax)
- [GitLive.net](https://gitlive.net)

## Install and setup
````
sudo apt install git       #Linux
brew install git           #MacOS
git init
````
## Git Large File Storage (LFS)
- [An open source Git extension for versioning large files](https://git-lfs.github.com/)
- [How to push large files to GitHub](https://ayunascode.medium.com/how-to-push-large-files-to-github-253d05cc6a09)
- [Configuring Git Large File Storage](https://docs.github.com/en/github/managing-large-files/configuring-git-large-file-storage)
````
brew install git-lfs       #MacOS
git lfs install
git lfs track "*.iso"
git add .gitattributes
git add file.iso
git commit -m "added file.iso"
git push origin main
````

## Various commands
Remember to unmount disk before checking. e.g. `umount /dev/sdb`
| Key/Command | Description |
| ----------- | ----------- |
| git clone https://github.com/Am0rphous/Cheat-Sheets | Clones the repo to your current folder |
| git add . | Adds all files in current folder to Change |
| git add fil1 fil2 fil3 | add files to Change |
| git commit -m "Describing the suff i did" | Commits a comment to all the files |
| git status |
| git push | Pushes everything up to Github |
| git push origin master |
| git log |

