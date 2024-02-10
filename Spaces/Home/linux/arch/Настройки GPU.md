## лучший опыт с использование prime
```
nvidia-inst -p
sudo pacman -S switcheroo-control ### ДЛЯ GNOME !!!!!!
sudo systemctl enable switcheroo-control.service
sudo systemctl start switcheroo-control.service
sudo systemctl status switcheroo-control.service
```
## просмотр полезной информации
```
lspci | grep 3050
lspci | grep VGA
glxinfo | grep "OpenGL renderer"
nvidia-smi
xrandr --listproviders
```
