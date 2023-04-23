# Pytorch CUDA Installation (WSL)

la prima cosa in assoluto è verificare di avere WSL

## 1. Installing CUDA on WSL

Make sure your WSL Ubuntu is updated.

In Windows: (powershell7 da amministratore)
* make sure WSL is updated `wsl.exe --update`
* make sure WSL is using Ubuntu version 2 `wsl.exe --set-version Ubuntu 2`

In Ubuntu:

```bash
sudo apt-get upgrade
sudo apt-get update
```

Install CUDA driver on WSL.
Make sure to select the proper version of CUDA.
At the moment of writing, pytorch last stable release is 2.0.0 which supports CUDA 11.8.
You can now look up pytorch stable versions [here](https://download.pytorch.org/whl/torch_stable.html).
CUDA insturctions for 11.8 [here](https://developer.nvidia.com/cuda-11-8-0-download-archive?target_os=Linux&target_arch=x86_64&Distribution=WSL-Ubuntu&target_version=2.0&target_type=deb_local))

To allow proper installations, it is suggested to properly add Eni certificates to WSL.
The list of certificates is available [here](https://trustedservices.eni.com/trusted/index.html#!/Info/CPS).
Download `eni Certification Authority (SHA2)` and `eni Root CA G1` certificates.
Within WSL, move to the Downloads folder and run the following command:

```bash
wget cert1
wget cert2
sudo mv *.crt /usr/local/share/ca-certificates/
sudo update-ca-certificates
```

It should add 2 new certificates.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda-repo-wsl-ubuntu-11-8-local_11.8.0-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-11-8-local_11.8.0-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-11-8-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda
rm -rf cuda-repo-wsl-ubuntu-11-8-local_11.8.0-1_amd64.deb
```

## 2. Installing conda

Install the last version of Anaconda/Miniconda (this can be done in parallel).

```bash
mkdir ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
~/miniconda3/bin/conda init bash
rm -rf ~/miniconda3/miniconda.sh
bash
```

## 3. Installing pytorch ----------> devi stare su wsl

Create a conda environment (at the moment, pytorch 2.0.0 supports up to python XXX):
```bash
conda create -n torchcuda python=3.9
```

Install pytorch
```bash
conda install pytorch torchvision torchaudio pytorch-cuda=11.8 -c pytorch -c nvidia
```

It may fail due to connection error/SSL error.
In this case, run:
```bash
export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt
```

You can now configure the environment with other needed libraries.

## Annex A. Troubleshooting

### Memory Usage
By default, WSL distributions will take up as much system memory as is available and not release it. This problem is compounded since Windows already takes up a decent chuck of memory. This seems to be something Microsoft is still working on. However, you can limit the amount of memory WSL can access. The workaround involves creating a .wslconfig file and adding it to you Windows user folder (e.g. `C:\Users\<Username>`). You can see the contents for an example config file below.

```
[wsl2]
memory=16GB
```
