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


O objetivo deste laboratório foi testar a autenticação no serviço SSH de um ambiente isolado (Kali Linux e Metasploitable) usando a ferramenta Medusa para entender os riscos de credenciais fracas ou padrão e como preveni-los. Na prática, aprendi a validar a conectividade com o alvo (ping), organizar wordlists, executar testes de força bruta e password spraying, aplicar medidas de defesa como senhas fortes e bloqueio de tentativas, e documentar o processo com evidências no GitHub.
