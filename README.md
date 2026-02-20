# Installing Python 3 Jupyter Notebook and Conda on Arch Linux
A guide to get Jupyter notebooks working in Visual Studio Code or Code OSS on Arch Linux and to fix the common issue where the notebook UI gets stuck searching for a kernel.

### Prerequisites
- Operating system: Arch Linux.
- Package manager: pacman and an AUR helper such as yay or paru.
- Editor: Visual Studio Code or Code OSS installed and available as /usr/bin/code.
- Python runtime: Anaconda or Miniconda installed at ~/anaconda3 (recommended for data science) or a Python venv.
- Jupyter tools: jupyter and ipykernel installed in the environments you want to use.
- Pip: sudo pacman -S --needed python-pip


Update system and AUR packages
```bash
# Update official repositories
sudo pacman -Syu

# Update AUR packages with yay
yay -Syu
yay -Sua
```

(Optional) If an AUR build fails, rebuild the failing package manually and inspect the build log
```bash
# Rebuild a failing AUR package
yay -S libdbusmenu-gtk2

# Or manual build
cd /tmp
git clone https://aur.archlinux.org/libdbusmenu-gtk2.git
cd libdbusmenu-gtk2
makepkg -si

```
Check your Python and Code version
```bash
python --version
code --version
# if needed: update them
```

Check your Extension version. Some people can fix the issue by loading the pre‑release version; I did not need that.
<img width="1490" height="399" alt="Screenshot_20260220_112525" src="https://github.com/user-attachments/assets/29ba7ece-9437-44a0-8221-54063f908ff1" />
<img width="1490" height="399" alt="Screenshot_20260220_112503" src="https://github.com/user-attachments/assets/16b5b7a6-a1f9-4c8c-ba39-a441e6df4378" />

Install Anaconda via:
https://www.anaconda.com/download
```bash
cd ~/Downloads
chmod +x Anaconda3-2024.11-Linux-x86_64.sh
./Anaconda3-2024.11-Linux-x86_64.sh
```

Create and register a Conda enviroment kernel
```bash
# Create and activate a Conda environment
conda create -n py310 python=3.10 -y
conda activate py310

# Install Jupyter and ipykernel
conda install -y ipykernel jupyterlab notebook

# Register the kernel for Jupyter and VS Code
python -m ipykernel install --user --name anaconda-py310 --display-name "Python (anaconda-py310)"

# Deactivate the environment to return the shell to its previous state.
# This prevents subsequent commands from accidentally running inside py310.
conda deactivate
```

(Optional) Create and register a venv kernel
```bash
python -m venv ~/venvs/vscode
source ~/venvs/vscode/bin/activate
pip install --upgrade pip ipykernel jupyter
python -m ipykernel install --user --name vscode --display-name "Python (venv vscode)"
deactivate
```

Verify Kernels
```bash
jupyter kernelspec list
cat ~/.local/share/jupyter/kernels/anaconda-py310/kernel.json
cat ~/.local/share/jupyter/kernels/vscode/kernel.json
# Ensure argv[0] is an absolute path to the environment's python
```
Use Ctrl+Shift+P Search ```Python: Select Interpreter``` and choose your Kernel
In my Case Conda
<img width="1043" height="422" alt="Screenshot_20260220_120512" src="https://github.com/user-attachments/assets/9f988dbb-687d-4d57-9c6c-148d52c35f96" />


## Close verything

Run in Terminal:
```bash
code --enable-proposed-api ms-toolsai.jupyter
```
Choose the Kernel

Close Code-Oss

Reinstalled the Extention with Terminal:
```bash
code --uninstall-extension ms-toolsai.jupyter
code --install-extension ms-toolsai.jupyter
```

Every time I opened Code the kernel was chosen.

!If you have to swap between kernels frequently (dont know why you would do that)
then you most likely want to create a .desktop launcher so it uses the command automatically:

```bash
mkdir -p ~/.local/share/applications
cat > ~/.local/share/applications/code-jupyter.desktop <<'EOF'
[Desktop Entry]
Name=Visual Studio Code (Jupyter Proposed API)
Comment=Start VS Code with proposed API enabled for ms-toolsai.jupyter
Exec=/usr/bin/code --enable-proposed-api ms-toolsai.jupyter %F
Icon=code
Type=Application
Terminal=false
Categories=Development;IDE;
StartupWMClass=Code
EOF

```
Warning: enabling proposed VS Code APIs is intended for extension development and can cause instability. Use the proposed API flag only temporarily or when you understand the tradeoffs.


### Removing Kernels

(Optional) Export environment before deleting:
```bash
conda activate py310
conda env export -n py310 > ~/py310-environment.yml
conda deactivate
```

Remove enviroment
```bash
conda remove -n py310 --all -y
# last resort (if conda remove fails)
rm -rf ~/anaconda3/envs/py310
```
Remove Jupyter-Kernel
```bash
jupyter kernelspec list
jupyter kernelspec remove anaconda-py310
```

If you exported it, restore the environment:
```bash
conda env create -f ~/py310-environment.yml
```
