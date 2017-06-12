# Postman Updater (for Linux)

This is a simple bash script, which allows you to update the Postman Linux app,
straight from the terminal.

## Installation

### 1. Using NPM
```bash
$ npm install -g postman-updater-linux
```

### 2. Manually

Copy the [`postman-updater`](https://github.com/czardoz/postman-updater-linux/blob/master/bin/postman-updater) script
to your $PATH, and make it executable. Remember to change the path, if you
do not want to install it to `/usr/local/bin`:

```bash
# curl -o /usr/local/bin/postman-updater https://github.com/czardoz/postman-updater-linux/blob/master/bin/postman-updater
# chmod +x /usr/local/bin/postman-updater
```

## Usage

By default, `postman-updater` assumes that the installation happens in `/opt/postman`. You can
override this by providing a command line flag, `-l /your/path`. If you are _not_ installing to any 
system directory, you will not have to use sudo in any of the following commands.

#### 1. Checking for updates

You can check if updates are available:
```bash
sudo postman-updater check
```

Or, with a custom install location:
```bash
postman-updater check -l /your/custom/path
```

#### 2. Installing Postman

You can install Postman to a path:
```bash
sudo postman-updater install # by default, installs to /opt/postman
```

Or, with a custom install location:
```bash
postman-updater install -l /your/custom/path
```

#### 3. Updating Postman

This will update Postman, with default installation directory set to `/opt/postman`.

```bash
sudo postman-updater update
```

You can change the default location with the `-l` flag:

```bash
postman-updater update -l /your/custom/path
```
