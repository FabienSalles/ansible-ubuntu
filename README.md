# Personal Ubuntu desktop setup 

## Intro 

Create for ubuntu 20.04 inspired by [vancrayelynghe/ansible-ubuntu](https://github.com/lvancrayelynghe/ansible-ubuntu)

## Installation

```
sudo apt-get install git
git clone https://github.com/FabienSalles/ansible-ubuntu.git
cd ansible-ubuntu
./install.sh
```

## Roles included

| Role | Description
| ------- | ------ |
| common | Install a lot of usefull packages (curl, htop, less, zip ... see [corresponding task file](https://github.com/FabienSalles/ansible-ubuntu/blob/master/roles/common/tasks/main.yml)) |
| desktop | Install a lot of usefull packages (meld, vlc, xclip, ...see [corresponding task file](https://github.com/FabienSalles/ansible-ubuntu/blob/master/roles/desktop/tasks/main.yml)) |