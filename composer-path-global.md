# Make laravel globally

### Open .bashrc file (if not avaible try .bash_profile)

```bash
sudo nano ~/.bashrc
```
### Add the following at the end of the file
Ubuntu:

```text
export PATH="~/.config/composer/vendor/bin:$PATH"
```

Mac:

```text
export PATH=~/.composer/vendor/bin:$PATH
```

### Then in terminal

```bash
source ~/.bashrc
```
