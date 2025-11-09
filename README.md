 Nessa primeira etape eu onfigurei duas máquinas virtuais: 

    Kali Linux (atacante)
    Metasploitable 2 (vítima — contém serviços vulneráveis como FTP, SMB e DVWA)
     

Ambas as VMs foram configuradas com rede Host-Only, garantindo comunicação direta entre elas sem expor o ambiente à internet.

<img width="576" height="213" alt="check-ping-machine-works" src="https://github.com/user-attachments/assets/844c5227-b28c-4f18-9497-856fe43bd1dd" />





🌐 Acesso ao DVWA (Damn Vulnerable Web App)


Após verificar que o ping ocorreu com sucesso abri o navegador e acessei o DVWA
<img width="1896" height="884" alt="dvwa_access" src="https://github.com/user-attachments/assets/22509c22-cc0d-4da0-b488-f92fb149144f" />

 
 Este ambiente será usado para simular um ataque de força bruta em formulário de login. 
 
💣 Etapa 3: Ataque de Força Bruta no DVWA (HTTP Form) 
Nessa etapa houve a criação de duas wordlists uma para passwords e outra para users, após criada foi utilizado o medusa para tentativas de login usando as wordlists.

 
 <img width="1412" height="350" alt="dvwa_test_users_and_pass" src="https://github.com/user-attachments/assets/1b28a644-46ae-47d6-b8e8-bb4257e001b3" />

✅ Resultado:
O Medusa testou todas as combinações de usuário e senha. Os seguintes acessos foram encontrados com sucesso: 

    user:123456
    msfadmin:123456
    admin:123456
    root:123456
     

Isso demonstra que o sistema DVWA, mesmo com autenticação, é vulnerável a ataques de força bruta quando não há proteção contra tentativas repetidas. 



📦 Ataque de Força Bruta no Serviço FTP

Primeiramente foi identificado através do mapeamento usando nmap que havia um serviço ftp presente.
<img width="1909" height="887" alt="nmap-command" src="https://github.com/user-attachments/assets/290c62de-421e-4b4e-aa0a-f9100d71163a" />

Após identificado, com os mesmos wordlists usados anteriormente foi feito a tentativa e descoberto as seguintes credenciais

    msfadmin:msfadmin 
     
<img width="1669" height="827" alt="medusa_users_and_pass_create_files" src="https://github.com/user-attachments/assets/9478e8f9-016b-409b-8b54-d68c8f17044d" />


Esse resultado confirma que o serviço FTP está configurado com credenciais padrão e sem qualquer tipo de proteção contra tentativas de login. 
 
✅ Validação do Acesso (FTP) 
 
<img width="571" height="220" alt="ftp_access" src="https://github.com/user-attachments/assets/5f5e2825-5317-4843-b62e-28cd6e2d2880" />

Ao inserir o usuário msfadmin e a senha msfadmin, recebi a mensagem: 

    230 Login successful. 
     



🛠️ Ataque de Password Spraying no Serviço SMB 


Primeiramente, tentei acessar manualmente o serviço SMB com o usuário msfadmin usando o comando smbclient mas recebi falha de autenticação.

<img width="576" height="213" alt="check-ping-machine-works" src="https://github.com/user-attachments/assets/844c5227-b28c-4f18-9497-856fe43bd1dd" />


Para descobrir quais usuários realmente existiam, usei a ferramenta enum4linux para enumerar informações do servidor SMB. O resultado revelou o nome do workgroup (WORKGROUP) e listou usuários válidos, como msfadmin, service e user. 
<img width="1219" height="822" alt="enumeracao" src="https://github.com/user-attachments/assets/1858a2dc-f981-4af8-96ee-2872dfce72b9" />

Com base nessa enumeração, criei uma nova wordlist de usuários (sub_users.txt) e outra de senhas comuns (senhas_spray.txt). Em seguida, executei o Medusa com o módulo smbnt para realizar um ataque de password spraying, testando poucas senhas em múltiplos usuários. 
<img width="1386" height="409" alt="list-users-and-passwords-medusa" src="https://github.com/user-attachments/assets/018e1852-0af4-4867-8c1c-fea06e6ac399" />

✅ Resultado:
O Medusa identificou com sucesso a combinação msfadmin:msfadmin, concedendo acesso ao compartilhamento SMB. 
<img width="883" height="418" alt="smb_access" src="https://github.com/user-attachments/assets/b1f36f1f-c765-427e-b976-3774c99d0445" />

Esse resultado confirma que o serviço SMB estava configurado com credencial padrão e sem proteção contra ataques de força bruta ou password spraying.
 
