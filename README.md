# Simulacao de Testes de Autenticacao e Medidas de Prevencao

Projeto de bootcamp Santander /DIO sobre laboratorio isolado e medidas de prevencao.

## Ambiente e Estrutura
-Laboratorio virtualizado: Kali Linux e Metasploitable 2 em rede Host-Only.
-Wordlistis em wordlists/usuarios/lista_usuarios.txt e lista_senhas.txt.
## Analise e Prevencao
-FTP e SMB: risco de credenciais fracas ou padrao e acesso administrativo em ambiente isolado.
-Prevencao: senhas fortes, MFA, rate limiting, bloqueio temporario e monitoramento de logs.

<img width="1920" height="983" alt="Screenshot_2026-11-21_12_24_29" src="https://github.com/user-attachments/assets/a2e2944e-52cc-4062-8688-09815f7d27d9" />
Teste de conectividade:
Verificação de comunicação de rede entre o Kali Linux e o alvo 192.168.56.101 com ping -c 3 192.168.56.101, confirmando 3 pacotes transmitidos, 3 recebidos e 0% de perda de pacotes.


<img width="1920" height="983" alt="Screenshot_2026-11-21_12_35_06" src="https://github.com/user-attachments/assets/e54a10a0-9245-49e0-b4e7-71158fd6eea4" />
Organização e conferência das Wordlists:
Listagem e inspeção em ~/projeto-medusa-dio/wordlists dos diretórios e arquivos lista_senhas.txt e lista_usuarios.txt usados para alimentar os testes de autenticação.


<img width="1920" height="983" alt="Screenshot_2026-11-21_12_36_24" src="https://github.com/user-attachments/assets/c2a58534-14d4-4203-9926-68ba6512b8eb" />
Força bruta tradicional com Medusa (-U e -P):
Execução do medusa -h 192.168.56.101 -U usuarios/lista_usuarios.txt -P usuarios/lista_senhas.txt -M ssh, testando combinações de usuário e senha no serviço SSH e identificando User: msfadmin Password: msfadmin [SUCCESS].


<img width="1920" height="983" alt="Screenshot_2026-11-21_12_39_49" src="https://github.com/user-attachments/assets/95a3e5fa-ac46-4fe5-8179-ce68f1566625" />
Password spraying com Medusa (-U e -p):
Execução do medusa -h 192.168.56.101 -U usuarios/lista_usuarios.txt -p msfadmin -M ssh, aplicando a senha msfadmin em múltiplos usuários da lista no alvo 192.168.56.101 para reduzir bloqueios por rrepetição em uma única conta.

<img width="1920" height="983" alt="Screenshot_2026-11-22_12_23_22" src="https://github.com/user-attachments/assets/54f27b8b-e393-4357-bd1f-67ea1cdfa4ac" />                                  Evidência — Teste de Autenticação em Serviço FTP (`192.168.56.101`)
Nesta captura, vemos o fluxo completo do teste de força bruta contra o serviço FTP no ambiente virtualizado e isolado (`192.168.56.101`) a partir do Kali Linux, utilizando o Medusa (-M ftp).

* **Preparação e conectividade:** Antes da execução, foram conferidas as *wordlists* de usuários e senhas (`lista_usuarios.txt` e `lista_senhas.txt`) e validada a comunicação na rede isolada via `ping` para o alvo `192.168.56.101`[cite: 1].
* **Execução do teste:** O comando `medusa -h 192.168.56.101 -U wordlists/usuarios/lista_usuarios.txt -P wordlists/usuarios/lista_senhas.txt -M ftp` realizou múltiplas tentativas de autenticação combinando os usuários e senhas cadastrados[cite: 2].
* **Resultado obtido:** O Medusa identificou uma credencial válida no serviço FTP do alvo (`User: msfadmin` / `Password: msfadmin` com retorno `[SUCCESS]`) e concluiu a varredura das demais combinações da lista[cite: 2].
* **Análise e mitigação:** O resultado evidencia o risco de manter senhas padrão/fracas, ausência de limitação de tentativas e uso de protocolos sem criptografia (FTP em texto claro). Na prática defensiva, recomenda-se migrar para **SFTP** ou **FTPS**, impor senhas fortes, habilitar bloqueio temporário por falhas repetidas (como *Fail2Ban*) e monitorar logs de autenticação.

<img width="1920" height="983" alt="Screenshot_2026-11-22_12_26_45" src="https://github.com/user-attachments/assets/f81a03d8-34f1-41e6-8e04-a0129920ad3e" />

<img width="1920" height="983" alt="Screenshot_2026-11-22_12_26_58" src="https://github.com/user-attachments/assets/636850b5-c29c-4d7e-8607-77399893802a" />

<img width="1920" height="983" alt="Screenshot_2026-11-22_12_29_44" src="https://github.com/user-attachments/assets/0f33642c-a2b1-44e5-9578-d7077f273e1b" />

### Evidência — Enumeração de Usuários e *Password Spraying* em SMB (`192.168.56.101`)

Nesta captura, vemos o processo de enumeração via **SMB** e a execução de *password spraying* contra o alvo isolado (`192.168.56.101`) a partir do **Kali Linux**.

* **Enumeração de usuários:** Com o comando `enum4linux -U 192.168.56.101`, foi verificada a permissão de sessão sem credenciais (`username ''`, `password ''`) e obtida a listagem de contas e respectivos RIDs do sistema, incluindo `msfadmin`[cite: 3, 4].
* **Preparação da lista e execução do *password spraying*:** Após conferir o arquivo `wordlists/usuarios/lista_usuarios.txt`, foi executado o comando `medusa -h 192.168.56.101 -U wordlists/usuarios/lista_usuarios.txt -p msfadmin -M smbnt` para testar uma senha comum (`msfadmin`) em diferentes usuários da lista usando o módulo SMB do Medusa[cite: 5].
* **Resultado obtido:** O teste identificou acesso válido para a conta `msfadmin` com a senha `msfadmin` (`[SUCCESS (ADMIN$ - Access Allowed)]`), enquanto os demais usuários verificados (`admin`, `user`, `root`, `guest`) não autenticaram com essa senha[cite: 5].
* **Análise e mitigação:** A exposição de informações via sessão anônima facilita a coleta de nomes de contas, e o uso de senhas fracas ou padrão amplia o risco de acesso administrativo indevido. Na defesa, recomenda-se desabilitar protocolos legados como o SMBv1, restringir sessões anônimas e enumeração excessiva no Samba/SMB, impor senhas fortes, aplicar o princípio do menor privilégio e monitorar eventos de falha e sucesso de logon.



## 🔐 Testes de Autenticação: Força Bruta, *Password Spraying*, FTP e SMB

> ⚠️ **Aviso:** Todos os procedimentos foram executados exclusivamente em laboratório virtualizado e isolado (`192.168.56.101`)[cite: 1, 2, 4, 5], com fins educacionais e de análise defensiva (*Blue Team*).

### 1. Força Bruta Tradicional com Medusa
* **Como funciona o teste:** Avalia a resistência da autenticação testando múltiplas combinações de usuários e senhas (`-U` e `-P`) de forma sistemática contra o serviço alvo[cite: 1].
* **Risco e impacto:** Quando não há limitação de tentativas, bloqueio progressivo ou senhas fortes, contas válidas podem ser descobertas por exaustão/combinação de listas[cite: 1].
* **Defesa e mitigação:** Aplicar políticas de senhas complexas, implementar *rate limiting* e bloqueio temporário por falhas consecutivas (ex.: *Fail2Ban*), e monitorar picos de falhas de autenticação nos logs.

### 2. *Password Spraying* com Medusa
* **Como funciona o teste:** Em vez de testar várias senhas em uma única conta, aplica **uma senha comum** (`-p`) em **vários usuários** (`-U`), reduzindo a chance de disparar bloqueios por conta individual rapidamente[cite: 5].
* **Risco e impacto:** Explora o uso de senhas padrão, fracas ou reutilizadas em múltiplas contas do ambiente, podendo resultar em acesso não autorizado em contas ativas ou administrativas[cite: 5].
* **Defesa e mitigação:** Proibir senhas padrão/comuns, exigir autenticação multifator (MFA) sempre que possível e correlacionar eventos de falha de logon em vários usuários a partir da mesma origem no SIEM.

### 3. Teste de Autenticação em FTP (`-M ftp`)
* **O que foi testado:** Verificação de autenticação no serviço FTP do host `192.168.56.101` utilizando as listas `lista_usuarios.txt` e `lista_senhas.txt` com o módulo `ftp` do Medusa[cite: 1].
* **Resultado observado:** Após validar a conectividade na rede isolada, a credencial `msfadmin` / `msfadmin` foi identificada como válida (`[SUCCESS]`)[cite: 1].
* **Defesa e mitigação:** Substituir o FTP tradicional (que trafega dados e credenciais em texto claro) por **SFTP** ou **FTPS**, remover credenciais padrão e monitorar tentativas de login no serviço.

### 4. Teste de Autenticação em SMB (`-M smbnt`)
* **O que foi testado:** Após a enumeração de usuários via `enum4linux -U 192.168.56.101` e a conferência de `lista_usuarios.txt`, foi executado o *password spraying* com a senha `msfadmin` no módulo `smbnt` contra `192.168.56.101`[cite: 2, 3, 4, 5].
* **Resultado observado:** A credencial `msfadmin` / `msfadmin` obteve acesso (`[SUCCESS (ADMIN$ - Access Allowed)]`), enquanto os demais usuários verificados (`admin`, `user`, `root`, `guest`) não autenticaram com essa senha[cite: 5].
* **Defesa e mitigação:** Desabilitar protocolos legados como SMBv1, restringir sessões anônimas e enumeração de contas no Samba/SMB, reforçar políticas de senha e auditar eventos de autenticação em serviços administrativos.







