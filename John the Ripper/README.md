# Instalando o John the Ripper

```bash
apt install john
# O que faz: Instala o John the Ripper no sistema.
```
- Detalhes: O comando apt é o gerenciador de pacotes do Debian/Ubuntu. Ele baixa e instala o JTR e suas dependências.

#

# Combinando /etc/passwd e /etc/shadow em um único arquivo

```bash
unshadow /etc/passwd /etc/shadow > unshadowed.txt

# O que faz: Combina os arquivos /etc/passwd e /etc/shadow em um único arquivo chamado unshadowed.txt.
```
- Detalhes:

- O /etc/passwd contém informações sobre os usuários, mas as senhas são armazenadas no /etc/shadow (em sistemas modernos).

- O comando unshadow junta esses dois arquivos para que o JTR possa processá-los.

- O arquivo resultante (unshadowed.txt) contém os hashes das senhas.

#

# Cracking em modo único (Single Crack Mode)

```bash
john --single --format=crypt unshadowed.txt
# O que faz: Tenta quebrar senhas usando o modo "single", que gera possíveis senhas com base nas informações do usuário (como nome de usuário, nome completo, etc.).
```
- Detalhes:

`--single`: Ativa o modo único.

`--format=crypt`: Especifica o tipo de hash (neste caso, crypt).

- O JTR tenta senhas óbvias, como o nome do usuário invertido ou com números adicionados.

#

# Ataque de força bruta e dicionário

```bash
john --wordlist=/usr/share/john/password.lst --rules unshadowed.txt --format=crypt
# O que faz: Usa uma lista de palavras (dicionário) para tentar quebrar as senhas.
```
- Detalhes:

`--wordlist=/usr/share/john/password.lst`: Especifica o arquivo de dicionário contendo possíveis senhas.

`--rules`: Aplica regras de transformação às palavras do dicionário (por exemplo, adicionar números, mudar letras maiúsculas/minúsculas).

`--format=crypt`: Define o tipo de hash.

- O JTR testa cada palavra do dicionário e suas variações contra os hashes.

#

# Mostrando hashes quebrados

```bash
john --show unshadowed.txt

# O que faz: Exibe as senhas que foram quebradas com sucesso.
```
- Detalhes:

- O JTR armazena as senhas quebradas no arquivo ~/.john/john.pot.

- O comando --show lê esse arquivo e exibe as senhas correspondentes aos usuários.

#

# Continuando uma sessão interrompida

```bash
john --restore
# O que faz: Retoma uma sessão de cracking que foi interrompida (por exemplo, se você pressionou Ctrl+C).
```
- Detalhes:

- O JTR salva o progresso em um arquivo de estado. O comando --restore carrega esse arquivo e continua de onde parou.

#

# Cracking apenas contas com shells específicos
```bash
john --wordlist=mydict.txt --rules --shell=bash,sh unshadowed.txt --format=crypt

#O que faz: Quebra senhas apenas para usuários que têm shells específicos (como bash ou sh).
```
- Detalhes:

`--shell=bash,sh`: Filtra os usuários que usam os shells bash ou sh.

- Útil para focar em contas que são mais prováveis de serem usadas por humanos.

#

# Cracking apenas algumas contas

```bash
john --wordlist=mydict.txt --rules --users=admin,mark unshadowed.txt --format=crypt

#O que faz: Quebra senhas apenas para usuários específicos (neste caso, admin e mark).
```
- Detalhes:

`--users=admin,mark`: Filtra os usuários listados.

- Útil para focar em contas de interesse.

#

# Cracking no modo incremental
```bash
john --incremental unshadowed.txt --format=crypt

# O que faz: Usa o modo incremental, que testa todas as combinações possíveis de caracteres.
```
- Detalhes:

`--incremental`: Ativa o modo incremental.

- Esse modo é muito poderoso, mas pode demorar muito, dependendo do tamanho e complexidade das senhas.

- O comportamento do modo incremental pode ser configurado no arquivo /etc/john/john.conf.

#

# Eliminando duplicatas de uma lista de palavras

```bash
john --wordlist=mydict.txt --stdout | sort | uniq > myunique.txt

# O que faz: Remove palavras duplicadas de uma lista de palavras.
```
- Detalhes:

`--wordlist=mydict.txt`: Especifica o arquivo de dicionário.

`--stdout`: Exibe as palavras na saída padrão.

`sort | uniq`: Ordena as palavras e remove duplicatas.

`> myunique.txt`: Salva o resultado em um novo arquivo.


### Legalidade: Use o JTR apenas em sistemas que você possui ou tem permissão para testar. Quebrar senhas sem autorização é ilegal.

### Desempenho: O tempo de cracking depende da complexidade das senhas e do poder de processamento do seu sistema.

### Dicionários: Use listas de palavras de qualidade para aumentar as chances de sucesso. Você pode encontrar listas em /usr/share/dict ou em ferramentas como o Metasploit (/usr/share/metasploit-framework/data/wordlists).

#

# Tipos de Hashes Suportados
- O JTR suporta uma variedade de algoritmos de hash. Para ver a lista completa, use:

```bash
john --list=formats
```
- Exemplos de hashes comuns:

`md5crypt`: Hashes MD5 (usados em sistemas Linux).

`sha512crypt`: Hashes SHA-512 (comuns em sistemas modernos).

`nt`: Hashes NT (usados no Windows).

`lm`: Hashes LM (antigos hashes do Windows, mais fracos).

`raw-md5, raw-sha1`: Hashes brutos (sem salt).

- Como especificar o formato:
Use o parâmetro --format. Por exemplo:

```bash
john --format=sha512crypt unshadowed.txt
```
#

# Modos de Ataque
- O JTR oferece vários modos de ataque para quebrar senhas. Vamos detalhar cada um:

1.  Modo Single (Único)
O que faz: Gera senhas com base nas informações do usuário (nome, sobrenome, etc.).

- Exemplo:

```bash
john --single --format=sha512crypt unshadowed.txt

# Quando usar: Quando você suspeita que os usuários usam senhas óbvias, como o nome do usuário ou variações.
```

2. Ataque de Dicionário
O que faz: Testa senhas a partir de uma lista de palavras (dicionário).

- Exemplo:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt unshadowed.txt
```
- Dicas:

- Use dicionários de qualidade, como rockyou.txt (disponível no Kali Linux).

- Combine com regras (--rules) para aumentar as chances de sucesso.

3. Ataque de Força Bruta
O que faz: Testa todas as combinações possíveis de caracteres.

- Exemplo:

```bash
john --incremental --format=sha512crypt unshadowed.txt

# Quando usar: Quando você não tem uma lista de palavras e quer testar todas as possibilidades.
```
- Cuidado: Pode demorar muito tempo, dependendo do tamanho e complexidade das senhas.

4. Ataque com Regras
O que faz: Aplica transformações às palavras do dicionário (por exemplo, adicionar números, mudar maiúsculas/minúsculas).

- Exemplo:

```bash
john --wordlist=rockyou.txt --rules --format=sha512crypt unshadowed.txt
```
- Regras comuns:

`l`: Converte para minúsculas.

`u`: Converte para maiúsculas.

`d`: Adiciona dígitos no final.

`$`: Adiciona caracteres especiais.

## Configuração Avançada
- O JTR é altamente configurável. O arquivo de configuração principal está em /etc/john/john.conf.

5. Modo Incremental Personalizado
Você pode criar modos incrementais personalizados no john.conf. Por exemplo:

```ini
[Incremental:MeuModo]
File = ~/meu-dicionario.txt
MinLen = 4
MaxLen = 8
CharCount = 26
```

- Como usar:

```bash
john --incremental=MeuModo --format=sha512crypt unshadowed.txt
```
6. Regras Personalizadas
Você pode criar suas próprias regras no john.conf. Por exemplo:

```ini
[List.Rules:MinhasRegras]
Az"[0-9]" ^[!@#]
```
- Explicação:

`Az`: Aplica maiúsculas e minúsculas.

`"[0-9]"`: Adiciona um número no final.

`^[!@#]`: Adiciona um caractere especial no início.

4. Exemplos Práticos
7. Quebrando Hashes de um Arquivo Específico
Suponha que você tenha um arquivo hashes.txt com os seguintes hashes:

```
admin:$6$saltsalt$hashhashhashhashhashhashhashhashhashhashhashhashhashhash
user1:$6$saltsalt$hashhashhashhashhashhashhashhashhashhashhashhashhashhash
```
- Comando para quebrar:

```bash
john --wordlist=rockyou.txt --format=sha512crypt hashes.txt
```
8. Quebrando Hashes do Windows (NTLM)
Se você tiver hashes NTLM (do Windows), use:

```bash
john --format=nt hashes_ntlm.txt
```
9. Usando Regras para Senhas Complexas
Se você suspeita que as senhas são complexas (por exemplo, "Senha123!"), use regras:

```bash
john --wordlist=rockyou.txt --rules --format=sha512crypt unshadowed.txt
```
#

# Boas Práticas e Dicas
10. Use GPUs para Acelerar o Processo
O JTR pode ser acelerado com GPUs. Instale a versão com suporte a GPU:

```bash
apt install john-opencl
```
- Verifique o suporte a GPU:

```bash
john --list=build
```
11. Combine Ferramentas
Use o Hashcat (outra ferramenta poderosa) em conjunto com o JTR para aumentar a eficiência.

12. Gere Seus Próprios Dicionários
Use ferramentas como Crunch ou Cewl para gerar listas de palavras personalizadas:

```bash
crunch 6 8 0123456789 -o numeros.txt
cewl http://exemplo.com -w dicionario.txt
```
13. Analise os Resultados
Depois de quebrar as senhas, analise os padrões (por exemplo, senhas comuns, senhas curtas) para melhorar a segurança do sistema.

#

# Exemplo Completo: Quebrando Hashes de um Sistema Linux
- Passo 1: Obtenha os Hashes
Combine /etc/passwd e /etc/shadow:

```bash
unshadow /etc/passwd /etc/shadow > unshadowed.txt
Passo 2: Execute o Ataque de Dicionário
```
- Use o dicionário rockyou.txt:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt unshadowed.txt
```
-Passo 3: Aplique Regras
Se o ataque de dicionário não for suficiente, aplique regras:

```bash
john --wordlist=rockyou.txt --rules --format=sha512crypt unshadowed.txt
```
-Passo 4: Use o Modo Incremental
Se ainda não tiver sucesso, use o modo incremental:

```bash
john --incremental --format=sha512crypt unshadowed.txt
```
- Passo 5: Verifique as Senhas Quebradas
Exiba as senhas quebradas:

```bash
john --show unshadowed.txt
```
#

# Recursos para Aprofundar
- Documentação Oficial: Leia a documentação do JTR (man john ou no site oficial).

- Livros:

- "Hacking: The Art of Exploitation" por Jon Erickson.

- "Penetration Testing: A Hands-On Introduction to Hacking" por Georgia Weidman.

- Pentester Academy.

- Offensive Security (OSCP).