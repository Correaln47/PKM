- Input remapper
- IsaacSim
- Isaac Lab
- Spotify
- [[Docker installation|Docker]]
- kitty terminal
- neovim - lazyvim
- [[Ros2 Installation|Ros2]]
- kicad
- vscode
Battery limit
sudo apt install tlp tlp-rdw
sudo systemctl enable tlp
sudo systemctl start tlp

sudo nano /etc/tlp.conf

START_CHARGE_THRESH_BAT0=40
STOP_CHARGE_THRESH_BAT0=80

sudo tlp start


