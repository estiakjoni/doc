## Aliases Terminal Commands in Ubuntu

**Example: Setting up an alias for the – sudo apt-get install – command**
<ul>
<li>Open the .bashrc file located in your home folder.

```bash
sudo nano .bashrc
```
</li>


<li>Move to the end of the file and paste the following line:

```bash
alias agi='sudo apt-get install'
```
Save the file by ctrl+x.

Here “agi” is the new alias we are setting up.
</li>

<li>Open the Terminal through Ubuntu Dash or by pressing <code>Ctrl+Alt+T</code>. Run the following command in order to start using the new bashrc file.

```bash
source ~/.bashrc
```
</li>

</ul>
