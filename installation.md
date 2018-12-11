# Install Joystream

This guide will explain how to install the `joyd` and `joycli` entrypoints onto your system. With these installed on a server, you can participate in the latest testnet as either a [Full Node](./join-testnet.md#run-a-full-node) or a [Validator](./validators/validator-setup.md).

## Requirements

Install [docker](https://docs.docker.com/engine/installation/) if you want to build from source.

Prepare a directory to install joyd and joycli into and add it to your PATH.

```bash
mkdir -p $HOME/bin
echo "export PATH=$PATH:$HOME/bin" >> ~/.bash_profile
```

## Install prebuilt binaries

  If you don't want to build from source you can download the binaries from:

  https://github.com/joystream/joystream-builder/releases

```bash  
  wget https://github.com/joystream/joystream-builder/releases/download/v0.28.0-rc2/linux_amd64.zip

  unzip linux_amd64.zip

  cp linux_amd64/* ~/bin
```

Jump to [verify everything is ok](#verify).

## Build from source
Lets build and install the latest version of Joystream from source.
Here we'll use a script, which will build the latest stable release.

```bash
cd ~
git clone https://github.com/joystream/joystream-builder
cd joystream-builder
./build.sh
```

That will build the binaries for windows, linux and osx platforms under the `bin/`. Copy your platform's binaries to your home bin directory, for example if you are on a linux server:

```bash
$ cp bin/linux_amd64/joy* ~/bin
```

<a href="verify"></a>
Verify that everything is OK:

```bash
$ joyd version
$ joycli version
```

## Run a Full Node

With the binaries installed, you can run [a full node on the latest testnet](./join-testnet.md).
