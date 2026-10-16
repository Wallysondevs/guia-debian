# Como escrever uma página do Guia Debian

O guia é uma **escola**, não uma lista de comandos. O leitor é iniciante: nunca mostre um comando completo sem antes explicar cada parte, o que ele devolve e o que fazer quando não funciona. A estrutura de aula é a mesma do Guia Ubuntu (`../guia-ubuntu/criar.html` é o modelo de profundidade). Mas **todo fato, comando e saída precisa ser do Debian 13**, conferido na VM de laboratório (seção 4).

## 1. Esqueleto do arquivo

```html
<!doctype html>
<html lang="pt-BR">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Debian TEMA — descrição curta | Guia Debian</title>
<meta name="description" content="Uma frase sobre a página.">
<link rel="stylesheet" href="assets/style.css">
<script>try{var t=localStorage.getItem("tema");if(t)document.documentElement.setAttribute("data-theme",t)}catch(e){}</script>
</head>
<body>
<div class="layout">
<nav class="sidebar" id="sidebar" aria-label="Tópicos"></nav>
<main class="main">
<div class="content">
<div class="pager"></div>
<h1>Debian <span class="hl">TEMA</span></h1>
… conteúdo …
<div class="pager"></div>
</div>
</main>
</div>
<script src="assets/site.js"></script>
</body>
</html>
```

O menu, o selo de nível (Nível 1/2/3) e os botões Anterior/Próximo são gerados por `assets/site.js` a partir da lista `NIVEIS`. Página nova = adicionar na `NIVEIS`.

## 2. Ordem de uma aula

1. **Intro** (`p.intro`): o que é, para que serve, em 2–3 frases.
2. **"Você vai aprender"** (`div.note` com lista de 3–5 itens).
3. **Conceito antes do comando**: explique a ideia (o que é um processo, uma permissão…) antes da sintaxe.
4. **Do simples ao completo**: primeiro a forma mais simples; depois **uma opção nova por vez**. Cada forma nova ganha uma `dl.anatomy` explicando cada parte.
5. **Toda saída explicada**: depois de cada sessão, diga o que o leitor está vendo. Use anotações `«` nas linhas importantes e tabelas para colunas (ex.: colunas do `ps aux`).
6. **O que o comando devolve**: saída + código de saída (`echo $?`), principalmente quando o comando não imprime nada (ex.: `pgrep` sem resultado devolve 1).
7. **Formas diferentes de fazer** a mesma tarefa, com tabela "Qual usar?".
8. **Experimente** (`div.try`) com missão, sempre que o simulador suportar o comando.
9. **Quando não funciona** (`div.cenario`): mensagem real → o que aconteceu → como confirmar → como resolver. Inclua o caso "não apareceu nada".
10. **Referência de opções** (`table.ref`).
11. **Exercícios** (3 ou mais: `.exercise` e `.mc`).
12. **Resumo** (lista curta).

## 3. Componentes

### Sessão de terminal (máquina fictícia)
```html
<pre class="term">$ ps
    PID TTY          TIME CMD
   3001 pts/0    00:00:00 bash        « o seu terminal
   3100 pts/0    00:00:00 ps          « o próprio ps
$ cd Documentos
$ ls
#> apt update
</pre>
```
- `$ ` = comando do usuário (vira `ana@notebook:~$`). `#> ` = comando como root (`root@notebook:~#`).
- O prompt acompanha os `cd` da sessão. Pasta inicial: `data-cwd="~/projeto"`.
- `data-user`, `data-host` (ex.: `data-host="servidor"`), `data-title` mudam a janela.
- `«` inicia uma anotação (aparece como etiqueta laranja, não é copiada).
- Comentário dentro da sessão: linha `# texto` (sem `$`) aparece como saída; prefira explicar no texto.
- Use `pre.file data-file="nome"` para mostrar o conteúdo de um arquivo (scripts, configurações).
- **Não use** `pre.cmd`/`pre.out` (formato antigo).

### Anatomia do comando
```html
<dl class="anatomy">
  <dt>pgrep</dt><dd>O programa: procura processos pelo nome.</dd>
  <dt>-a</dt><dd>Mostra a linha de comando completa.</dd>
  <dt>chrome</dt><dd>O nome a procurar.</dd>
</dl>
```

### Experimente (simulador interativo)
```html
<div class="try" data-setup="mkdir semestre;;cd semestre" data-goal="dir:~/semestre/jan,dir:~/semestre/fev">
<p class="try-task"><strong>Missão:</strong> crie as pastas jan e fev.</p>
</div>
```
- `data-setup`: comandos rodados antes, separados por `;;`.
- `data-goal` (todas precisam ser verdade): `dir:CAMINHO`, `file:CAMINHO`, `absent:CAMINHO`, `cwd:CAMINHO`, `contains:CAMINHO=texto`, `noproc:nome`, `ran:regex`.
- Garanta que a missão **não** já esteja cumprida pelo setup.

**O que o simulador sabe fazer:** `pwd cd pushd popd ls tree mkdir touch rmdir rm cp mv cat head tail wc grep sort uniq cut tr find echo printf seq xargs chmod date whoami hostname id groups uname uptime lsb_release free df history clear type which alias unalias export env printenv source test [ [[ sudo exit ps pgrep pkill kill killall help` + pipes, `> >> 2> 2>&1 < /dev/null`, `; && ||`, `$VAR`, `${VAR…}` (`${#v}`, `${v%.*}`, `${v:-x}`, `${v/a/b}`), `$(cmd)`, `$((conta))`, `$?`, chaves, curingas, `for …; do …; done`, `if …; then …; else …; fi`, funções de uma linha `f() { …; }`, Tab, histórico, `sudo -i`.
**Não sabe:** apt, systemctl, redes, editores, chown, tar, sed, awk, ln, du, `while`, `read`… Para esses, use só `pre.term` (nunca finja um `.try`). **Debian:** não há `sudo`; use `su -` (o simulador aceita a senha) e `exit`.

**Máquina fictícia (estado inicial):** usuária `ana` (grupos ana cdrom floppy audio dip video plugdev users netdev, sem sudo), máquina `notebook`, pasta `/home/ana` com `Área de trabalho/ Documentos/ Downloads/ Imagens/ Modelos/ Música/ Público/ Vídeos/ frutas.txt .bashrc .profile`; `Documentos/` tem `notas.txt acesso.log relatorio.odt projetos/{backup.sh, site/{index.html, style.css}}`; `Downloads/` tem `ubuntu-26.04-desktop-amd64.iso manual.pdf foto.jpg musica.mp3 fotos-viagem.zip`; `Imagens/` tem `ferias.jpg perfil.png`. `/etc` tem `hostname hosts os-release passwd shadow apt/`. `/root` é proibido para a ana. Processos: systemd(1), NetworkManager(640), gnome-shell(1534), firefox-esr(2210), Isolated Web Co(2251, uma aba do Firefox), nautilus(2304), gnome-text-edit(2388), gnome-terminal-(2950), bash(3001). Arquivos do Debian: `/etc/os-release`, `/etc/debian_version`, `/etc/apt/sources.list`, `/etc/network/interfaces`.

### Outros blocos
| Bloco | Uso |
| --- | --- |
| `div.note` / `div.tip` / `div.warning` | Nota / dica / perigo (todo comando destrutivo leva `warning`) |
| `div.cenario` | Problema real: `<strong>título</strong>` + sessão + o que aconteceu + como resolver |
| `ol.passo` | Passo a passo numerado (projetos, procedimentos) |
| `div.table-wrap > table.ref` | Tabelas |
| `.exercise > .ex-code > input[data-answer="a\|b"]` | Exercício de completar |
| `.mc[data-answer="2"]` | Múltipla escolha (posição da certa, começando em 1; `name` único) |

## 4. Fidelidade ao Debian 13 "trixie" — use a VM

Existe uma VM Debian 13.7 real só para este guia. **Confira nela toda saída que você publicar.**

```
K="$HOME/VirtualBox VMs/debian-guia/lab_ed25519"
ssh -q -p 2222 -i "$K" -o BatchMode=yes -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ana@127.0.0.1 'comando'   # usuária comum
ssh -q -p 2222 -i "$K" -o BatchMode=yes -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@127.0.0.1 'comando'  # root
```
Nos exemplos do guia, troque `10.0.2.15`/`enp0s3` (rede da VM) por `192.168.0.15`/`enp1s0` e mantenha `ana@notebook`. Para servidores, use `data-host="servidor"`.

**Regras do laboratório (a VM é compartilhada por vários autores ao mesmo tempo):**
- Pode instalar pacotes (`apt-get install -y …` como root) e criar arquivos em pastas temporárias.
- **Nunca** corte o acesso SSH: nada de ativar firewall sem liberar a porta 22 antes, mexer em `/etc/network/interfaces`, `sshd_config`, desligar/reiniciar a VM, mexer no `/etc/fstab` ou no GRUB de verdade. Para esses temas, faça em cópias (`/tmp/teste/fstab`), em arquivos-imagem com `losetup`, ou mostre a saída típica.
- Não apague nem altere usuários existentes; crie usuários de teste com nomes próprios (`teste1`…) e remova no fim.
- Discos/partições/LVM/RAID/ZFS: só em arquivos-imagem (`truncate -s 1G /tmp/disco1.img` + `losetup`).
- Se algo quebrar, pare e relate (existe um snapshot "instalacao-limpa" para restaurar).

**Fatos já conferidos (diferenças para o Ubuntu):**

| Ponto | Debian 13 na VM |
| --- | --- |
| Versão | Debian GNU/Linux 13 (trixie), 13.7; kernel 6.12.111+deb13-amd64; systemd 257; GRUB 2.12 (menu metade em inglês) |
| Administração | instalação com senha de root ⇒ **sudo não instalado**; ana **fora** do grupo sudo; usa-se `su -` ("Senha:", erro "su: Falha de autenticação") |
| PATH da ana | `/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games` (sem /sbin ⇒ `reboot`, `adduser`, `usermod` dão "bash: reboot: comando não encontrado") |
| Grupos da ana | `ana cdrom floppy audio dip video plugdev users netdev`; `id` mostra `grupos=` |
| Pasta pessoal | `drwx------` (700) |
| .bashrc | só `alias ls='ls --color=auto'`; `ll`, `la`, `l` e `grep --color` vêm **comentados**; prompt colorido igual ao do Ubuntu |
| coreutils | GNU 9.7: `ls -l` com mês em português (`out 15 20:57`), ordem pelo idioma (maiúsculas/minúsculas misturadas, acentos e pontos ignorados) |
| Mensagens | `mkdir: cannot create directory ‘a’: Arquivo existe` (prefixo em inglês!), `rmdir: falhou em remover 'x': Diretório não vazio`, `touch: não foi possível tocar 'x': Permissão negada`, `ls: não foi possível acessar 'x': Arquivo ou diretório inexistente`, `head: não foi possível abrir 'x' para leitura: …`, `chmod: alterando permissões de 'x': Operação não permitida`, `id: ‘x’: usuário inexistente`, `bash: x: comando não encontrado` (sem command-not-found) |
| apt | apt 3; `sources.list` no formato de uma linha: `deb http://deb.debian.org/debian trixie main non-free-firmware` (+ security e updates); `apt install` sem root: `Erro: Não foi possível abrir arquivo de trava … você é root?` |
| Firewall | nada ativo; `nft` instalado; `ufw` e `iptables` não instalados |
| Rede | ifupdown (`/etc/network/interfaces`, `allow-hotplug` + `dhcp`), dhcpcd gera o `/etc/resolv.conf`; sem NetworkManager no servidor |
| Logs | sem rsyslog: não existe `/var/log/syslog`; use `journalctl` (ana vê só os próprios logs: "Hint: You are currently not seeing messages from other users…") |
| Serviços ativos | ssh, cron, apparmor, networking, systemd-timesyncd; timers apt-daily, logrotate, fstrim, man-db |
| SysRq | `kernel.sysrq = 438` (de `/usr/lib/sysctl.d/50-default.conf`): libera S, U, B e também R; **E e I continuam bloqueadas** (64 não está incluso) |
| /tmp | é `tmpfs` (some ao reiniciar) |
| killall | não instalado (pacote psmisc) |
| Desktop (tasksel) | GNOME, Xfce, KDE Plasma, Cinnamon, MATE, LXDE, LXQt, GNOME Flashback; navegador recomendado firefox-esr (processo `firefox-esr` e abas `Isolated Web Co`); terminal `gnome-terminal` (processo `gnome-terminal-`) ou `gnome-console` |

O simulador (`assets/sim.js`) já imita esses fatos. Para gerar saídas exatas de `ls`:
`node -e "const S=require('./assets/sim.js');const sh=new S.Shell();['mkdir x','ls'].forEach(l=>sh.rodar(l).forEach(r=>process.stdout.write((r.texto||'').replace(/\u0001./g,'').replace(/\u0002/g,''))))"`

## 5. Escrita

- Português do Brasil, frases curtas, um assunto por parágrafo.
- Explique a origem em inglês de cada comando (`mkdir` = *make directory*).
- Termo técnico novo → explique na hora ou link para `glossario.html`.
- Dentro de qualquer `<pre>`, escreva `&lt;` `&gt;` `&amp;`.
- Links internos só para páginas que existem na `NIVEIS`.
