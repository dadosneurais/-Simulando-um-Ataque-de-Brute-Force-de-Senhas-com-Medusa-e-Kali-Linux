# Projeto kali, metasploitable2, DVWA exploit com Burpsuite
![](https://hermes.dio.me/users/company/3a52d6e3-a58c-4755-89c9-fbc093a8868f.png)
## Links para downloads:

[DVWA](https://github.com/digininja/DVWA)  
[VMWARE-Kali](https://cdimage.kali.org/kali-2025.4/kali-linux-2025.4-vmware-amd64.7z)  
[Metasploitable-2](https://sourceforge.net/projects/metasploitable/files/latest/download)  

## instalando Docker

```bash
sudo apt install docker.io -y
sudo apt install docker-compose -y
sudo docker compose up -d
```
## Visualizando e acessando o docker bash

```bash
sudo docker ps
sudo docker exec -it [CONTAINER ID] bash
```

### Verificando portas abertas em Metasploitable2
![ports](img/metasploitable2.png)
---

### Ataque FTP no Metasploitable2
![ftp](img/ftp.png)

### Verificando usuários SMB com enum4linux e salvando o resultado em e.txt
```bash
enum4linux -a 192.168.1.15 | tee e.txt
```
![enum](img/enum4linux_user.png)  
Posteriormente, crie um user.txt com esses usuários e utilize uma lista pass.txt para brute force!

### Acacando SMB com medusa
Utilizei user.txt e pass.txt com as mesmas credênciais, na perspectiva de pegar valores repetidos:  
```bash
medusa -h 192.168.1.15 -U user.txt -P pass.txt -M smbnt -t2 -T50
```
Filtre os resultados no terminal com Ctrl+Shift+F e pesquise "FOUND":  
![res](img/smbnt_acc_found.png)  
Com isso, foi identificado dois logins. Acessando via SMB:  
![res2](img/smbclient_login.png)  

---

## Atacando Servidor DVWA com burpsuite:  
Intercepter o login, realize algum aleatório para iniciar o ataque com Intruder:
![dvwa](img/dvwa_login.png)  

No burpsuite, insira uma lista de password no Payload:
![burp](img/burp_intercept.png)  

A parte mais interessante. É comum todo acesso inicar em uma página chamada index. Portanto, após realizado o ataque de Brute Force, procure pela palavra chave index, com isso você irá descobrir a senha que acessou a página inicial:  
SENHA: password  
![welcome](img/intruder_filter_index.png)  

O Brute force com medusa ou hydra não é eficiente nesse login, pois, a cada tentativa é gerado um novo user_token, o que invalida o teste. Desse modo, o burpsuite destacou-se nessa exploração de login.
