# what?
Brew is the classic package manager that everyone used for Mac books, turns out its been ported to Linux 
Yes you can just apt-get install etc, but brew tends to be be slicker and if you are supporting multiple platforms
for devlopers, its easier to just have 1 wiki

## install brew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
echo >> /home/chris/.bashrc
echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> /home/chris/.bashrc
eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
sudo apt-get install build-essential
brew install gcc

## install helm
brew install helm

## install kind
brew install kind

## install kubectl
brew install kubectl