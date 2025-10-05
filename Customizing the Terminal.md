Install kitty

With `kitten themes` Change the theme

install zsh with apt

set the shell with chsh -s $(which zsh)

after reopening the temrinal or rebooting select option 0

Install ohmyzsh
	sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

Starship

curl -sS https://starship.rs/install.sh | sh
add the follwoing line to the .zshrc file

eval "$(starship init zsh)"

for further starship config
`mkdir -p ~/.config && touch ~/.config/starship.toml`

For easier config:
`git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k`

	and in zshrc paste this ZSH_THEME="powerlevel10k/powerlevel10k"

for custom funny animals quotes
MY_COWS=(
    "tux"
    "dragon"
    "stegosaurus"
    "fox"
)

# 2. Get the total number of items in the array.
NUM_COWS=${#MY_COWS[@]}

# 3. Select a random cow from the array.
```sh
MY_COWS=(
    "tux"
    "dragon"
    "stegosaurus"
    "fox"
    "dragon-and-cow"
    "default"
    "www"
    "three-eyes"
    "suse"
    "bud-frogs"
)

# 2. Get the total number of items in the array.
NUM_COWS=${#MY_COWS[@]}

RANDOM_COW=${MY_COWS[$(( (RANDOM % NUM_COWS) + 1 ))]}

# 4. Run the final command with the randomly selected cow.
curl -s "Accept: text/plain" https://icanhazdadjoke.com/ | cowsay -f $RANDOM_COW | lolcat --freq=0.075 --force
```

p10k configure