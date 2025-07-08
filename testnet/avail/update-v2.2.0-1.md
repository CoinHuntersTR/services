# Update v2.3.1

**Release Information:**

* **Binary Version:** v2.3.1.0-rc1
* **Node Version:** v2.3.1

```
cd avail
```

### Dosyaları çekiyoruz

```
wget https://github.com/availproject/avail/releases/download/v2.3.1.0-rc1/x86_64-ubuntu-2204-avail-node.tar.gz
```

```
tar -xf x86_64-ubuntu-2204-avail-node.tar.gz
```

### Sevisi Çalıştırıyoruz...

```
screen -r avail
```

```
./avail-node --chain turing --name [name your node] --validator
```
