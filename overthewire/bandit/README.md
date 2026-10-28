# OverTheWire — Bandit

**Plataforma:** [OverTheWire](https://overthewire.org/wargames/bandit/)
**Host:** `bandit.labs.overthewire.org` | **Porta:** `2220`
**Objetivo:** Wargame focado em fundamentos de Linux — navegação, permissões, compressão, encoding e redirecionamento.

---

## Índice

| Nível | Conceito principal | Status |
|---|---|---|
| [0](#nível-0) | Conexão SSH básica | ✅ |
| [0 → 1](#nível-0--1) | Leitura de arquivo | ✅ |
| [1 → 2](#nível-1--2) | Arquivo com nome especial (`-`) | ✅ |
| [2 → 3](#nível-2--3) | Arquivo com espaços no nome | ✅ |
| [3 → 4](#nível-3--4) | Arquivo oculto em diretório | ✅ |
| [4 → 5](#nível-4--5) | Identificar arquivo legível entre vários | ✅ |
| [5 → 6](#nível-5--6) | `find` com filtros (tamanho, executável) | ✅ |
| [6 → 7](#nível-6--7) | `find` com filtros (usuário, grupo) + silenciar erros | ✅ |
| [7 → 8](#nível-7--8) | `grep` para busca por palavra | ✅ |
| [8 → 9](#nível-8--9) | `sort` + `uniq` para linha única | ✅ |
| [9 → 10](#nível-9--10) | `strings` + `grep` em arquivo binário | ✅ |
| [10 → 11](#nível-10--11) | Decodificação Base64 | ✅ |
| [11 → 12](#nível-11--12) | Cifra ROT13 | ✅ |
| [12 → 13](#nível-12--13) | Hexdump reverso + descompressão em cadeia | ✅ |
| [13 → 14](#nível-13--14) | — | 🔄 Em progresso |
| [14 → 33](#nível-14) | — | ⬜ Pendente |

---

## Nível 0

**Conceito:** Conexão SSH com usuário e porta customizada.

### Anotações
Para passar o Nível 0 só precisei definir o `login_name` para `bandit0` com `-l` e trocar a porta padrão pela porta `2220` com `-p`.

### Comando
```bash
ssh -l bandit0 -p 2220 bandit.labs.overthewire.org
```

### Credenciais
| Campo | Valor |
|---|---|
| Usuário | `bandit0` |
| Senha | `bandit0` |

---

## Nível 0 → 1

**Conceito:** Leitura de arquivo com `ls` e `cat`.

### Anotações
Bastou usar o `ls` para enxergar o arquivo `readme` e o `cat` para exibir a senha.

### Comando
```bash
ls readme && cat readme
```

### Senha obtida
```
6y2kwnwK6grgvwvpvLaa2T1cpFEKOhNR
```

---

## Nível 1 → 2

**Conceito:** Leitura de arquivo com nome especial iniciado por hífen (`-`).

### Anotações
O comando do nível anterior não bastava pois o arquivo era nomeado com um hífen no começo. Para manipular esse tipo de arquivo é necessário usar `./`, `--`, `<` ou renomear o arquivo — caso contrário o shell interpreta o hífen como um flag de opção.

### Comando
```bash
cat ./-
```

### Senha obtida
```
PK8fYLZg2hnHSz83plBL1iEPKdD3QToB
```

---

## Nível 2 → 3

**Conceito:** Leitura de arquivo com espaços no nome.

### Anotações
Precisei usar aspas para que o Linux tratasse o nome do arquivo inteiro como um único argumento, e não como várias palavras separadas. Combinei com o `./` do nível anterior.

### Comando
```bash
cat ./'spaces in this filename'
```

### Senha obtida
```
7ZZ2LFrykP2zEyvBl4m3clcL7tGYJPME
```

---

## Nível 3 → 4

**Conceito:** Encontrar arquivo oculto dentro de um diretório.

### Anotações
Precisei encontrar um arquivo oculto dentro do diretório `inhere`. Arquivos ocultos no Linux começam com `.` e ficam invisíveis para um `ls` comum — o `-a` exibe todos, incluindo os ocultos.

### Comandos
```bash
cd inhere
ls -a
# ou
find . -name ".*"
```

### Senha obtida
```
xzTXq1rDJQVVAzdv5cHq1TQytTWufAMq
```

---

## Nível 4 → 5

**Conceito:** Identificar o único arquivo com texto legível entre vários.

### Anotações
Entrei no diretório indicado e testei arquivo por arquivo. O truque seria usar `file` para identificar o tipo de cada um sem precisar abrir manualmente.

### Comandos
```bash
cd inhere
cat ./-file07
# alternativa mais eficiente:
file ./-file*
```

### Senha obtida
```
6C7h9GD8M6ai5nr7wo1RonrzFjj9yIrG
```

---

## Nível 5 → 6

**Conceito:** `find` com filtros de tamanho e permissão.

### Anotações
Precisei buscar um arquivo com critérios específicos. Descobri que o `find` permite definir filtros como tamanho e se o arquivo é executável ou não. O sufixo `c` no tamanho garante a busca por número exato de bytes.

### Comando
```bash
find . -type f -size 1033c ! -executable
```

> `c` = bytes exatos | `! -executable` = não executável

### Senha obtida
```
pXa26xhMWaC2SvDotA4r9EgZkulOeSBW
```

---

## Nível 6 → 7

**Conceito:** `find` com filtros de usuário e grupo + redirecionamento de erros.

### Anotações
Precisei aprofundar mais na busca filtrando por usuário, grupo e tamanho. O retorno inicial trazia vários arquivos com "permission denied", criando ruído e escondendo o arquivo correto. Resolvi acrescentar `2>/dev/null` para silenciar os erros e ver só o resultado útil.

### Comando
```bash
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

> `2>/dev/null` redireciona o `stderr` (erros) para o nada, limpando a saída.

### Senha obtida
```
Bmnnvf82KzQlfxgAI2d1zYbr1u9pr3E3
```

---

## Nível 7 → 8

**Conceito:** Busca por palavra específica em arquivo com `grep`.

### Anotações
Precisei aprender a usar o `grep` para localizar uma palavra específica dentro de um arquivo grande. O `-w` garante que só palavras inteiras sejam correspondidas (evita falsos positivos em substrings).

### Comando
```bash
grep -w 'millionth' data.txt
```

> `-w` = word match (palavra completa, não substring)

### Senha obtida
```
VR1ljMayciFxbnUokuQmJFw6QC9VKtub
```

---

## Nível 8 → 9

**Conceito:** `sort` + `uniq` para encontrar linha que aparece uma única vez.

### Anotações
Precisei entender como `sort` e `uniq` funcionam juntos. O `sort` organiza as linhas em ordem para que as duplicatas fiquem adjacentes, e o `uniq -u` filtra mostrando apenas as linhas que aparecem uma única vez. O `|` (pipe) encadeou os dois comandos na mesma linha.

### Comando
```bash
sort data.txt | uniq -u
```

> `|` = pipe, passa a saída de um comando como entrada do próximo | `-u` = somente linhas únicas

### Senha obtida
```
EjmOSvuAu7sGAHqHVcBDPirRe9T03kxl
```

---

## Nível 9 → 10

**Conceito:** Extrair strings legíveis de arquivo binário com `strings` + `grep`.

### Anotações
O arquivo `data.txt` continha uma mistura de strings binárias e strings legíveis. Usei `strings` para filtrar apenas o texto legível e depois `grep` para encontrar as linhas que continham `=` (pista dada pelo enunciado).

### Comando
```bash
strings data.txt | grep -E '='
```

### Senha obtida
```
B0s2khmbT9u0geKuOoVGW3JZKhndE3BG
```

---

## Nível 10 → 11

**Conceito:** Decodificação Base64.

### Anotações
O arquivo `data.txt` estava codificado em Base64. Usei o `base64` com a flag `-d` para decodificar.

### Comando
```bash
base64 -d data.txt
```

> `-d` = decode (decodificar)

### Senha obtida
```
pYfOY6HwUsDj5rL9UvyhU7MCmv8vN5Ro
```

---

## Nível 11 → 12

**Conceito:** Cifra ROT13 com `tr`.

### Anotações
Precisei aprender o que é ROT13: uma cifra que rotaciona cada letra 13 posições no alfabeto latino básico (A-Z / a-z). Para reverter, basta aplicar o mesmo algoritmo novamente (já que 13 + 13 = 26, volta ao início). Usei o comando `tr` (translate) para fazer a substituição.

### Comando
```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

> `tr` = translate — substitui caracteres de acordo com o mapeamento definido

### Senha obtida
```
GROozWPO8QyN0mGrjUkID0WCYkZiQxrN
```

---

## Nível 12 → 13

**Conceito:** Reverter hexdump + descompressão em cadeia (gzip, bzip2, tar).

### Anotações
Este foi o nível mais trabalhoso até aqui. O arquivo `data.txt` era um hexdump de um arquivo que havia sido comprimido várias vezes em sequência. O processo foi:
1. Criar um diretório temporário em `/tmp`
2. Copiar o arquivo para lá
3. Reverter o hexdump com `xxd -r`
4. Usar `file` repetidamente para descobrir o tipo de compressão atual
5. Renomear e descompactar em loop até chegar no arquivo final

### Comandos
```bash
# Criar diretório temporário
mktemp -d

# Copiar o arquivo original
cp data.txt /tmp/tmpXXXXXX/data_copy.txt

# Reverter o hexdump
xxd -r data.txt > data.bin

# Identificar o tipo do arquivo gerado
file data.bin

# Descompactar conforme o tipo identificado (repetir até chegar no texto)
mv data.bin data.gz  && gzip -d data.gz
mv data     data.bz2 && bzip2 -d data.bz2
tar -xf data.tar
```

> O ciclo `file → renomear → descompactar` se repete várias vezes até o arquivo final ser texto puro.

### Senha obtida
```
qQYQiHOBPR8zR61qxYqX45quvihF2uzk
```

---

## Nível 13 → 14

> 🔄 Em progresso

---

*Os níveis seguintes serão preenchidos conforme avanço no wargame.*
