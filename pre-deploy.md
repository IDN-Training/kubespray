# on deployment ansible

Note: Use deploy user

### install python dependecies
```
sudo apt install python3-dev libffi-dev gcc libssl-dev && sudo apt install python3-pip python3-venv && sudo pip3 install -U pip
```

### download kubespray
```
git clone https://github.com/kubernetes-sigs/kubespray.git
```

### create python venv
```
python3 -m venv kubespray/
```

### activate venv
```
cd kubespray/
source bin/activate
```

### install add on dependecies
```
git checkout v2.22.1
pip install -r requirements.txt
```