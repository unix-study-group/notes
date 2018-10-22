Problema
===

Toda vez que eu tinha que formatar meu linux, sempre vinha aquele veeeelho pensamento: "Pô, vou ter que instalar tudo de novo, vou perder muito tempo nisso... Vou deixar assim mesmo kkkk". Depois de um tempo comecei a fazer o seguinte questionamento: "Será que dá para fazer um script para automatizar essa instalação?".
Bem, foi a partir disso que fui pesquisar sobre formas de como fazer isso acontecer.

Análise
===

Primeiramente fui atrás de uma linguagem que me possibilitasse realizar essa automatização. Com isso, conheci o shellscript. Uma boa amostra do poder dessa linguagem é que ela te possibilita fazer instalações e execução de scripts de forma automática, além da possibilidade de geração de relatórios e testes.
Dito isso, eu teria que ver uma forma do meu script ler algum arquivo de texto que possuísse os nomes dos pacotes que eu queria instalar. Lendo mais sobre alguns comandos básico do linux, cheguei na seguinte solução...

Solução
===

Bem, o algoritmo está estruturado nas seguintes etapas:
* Exibição de informações sobre o seu sistema opercional e qual é a sua arquitetura. 
    
* Atualização do sistema:
    * Atualiza a sua lista de repositórios utilizando o **apt update**;
    * Atualiza os pacotes já instalados utilizando o **apt upgrade**;
    * Atualiza de forma mais profunda, tentando atualizar pacotes para as versões
	mais recentes e removendo dependências mais velhas ou não utilizadas, através do **apt full-upgrade**.

* Leitura do arquivo com extensão .txt que o usuário irá passar como parâmentro na execução do shellcript.
* Logo após isso, o algoritmo executa um for com todos os nomes que estão armazenados no arquivo de texto fornecido anteriormente.
    * Caso a instalação seja mal sucedida, será retornado na tela a mensagem "Não foi possível instalar o pacote (nome do pacote) :(".
    * Caso a instalação seja bem sucedida, será retornado na tela a mensagem "Pacote (nome do pacote) instalado com sucesso.".

* Mensagem informando que o código foi finalizado.

Caso ocorra algum erro tanto na atualização do sistema ou na instalação dos pacotes, o algoritmo retornará uma mensagem de erro.

```
#!/bin/bash
echo ==================== Informações do sistema ====================

lsb_release -a

echo Arquitetura do seu sistema:
getconf LONG_BIT

echo ================================================================

echo "Atualizando lista de repositórios..."
echo "+ apt update"
if ! apt update
then
    echo "Não foi possível atualizar os repositórios. Verifique seu arquivo /etc/apt/sources.list"
    exit 1
fi
echo "Atualização feita com sucesso."

echo ================================================================  

echo "Atualizando pacotes já instalados..."
echo "+ apt ugrade -y"
if ! apt upgrade -y
then
    echo "Não foi possível atualizar pacotes :("
    exit 1
fi
echo "Atualização de pacotes feita com sucesso."

echo ================================================================  

echo "Atualizando o seu sistema..."
echo "+ apt full-ugrade -y"
if ! apt full-upgrade -y
then
    echo "Não foi possível atualizar o sistema :("
    exit 1
fi
echo "Atualização do sistema realizada com sucesso..."

echo ================================================================  

for i in `cat $1`; 
    # note que $1 aqui será substituído pelo Bash pelo primeiro argumento passado em linha de comando
    do 
        if ! apt install $i -y;
        then
            echo "Não foi possível instalar o pacote $i :("
            exit 1
        fi
        echo "Pacote $i instalado com sucesso."

        echo ================================================================
done

echo "Programa finalizado."
```

Considerações
===

Como criei esse script para funcionar em sistemas que possuem o apt/apt-get como gerenciador de pacotes, eles fatalmente não vão funcionar em outros. 
Algumas das futuras mudanças que poderiam ser feitas seriam melhorar a qualidade das mensagens de erros, além de tornar esse script "portável" para outros sistemas baseado no linux e que possuem gerenciadores de pacotes diferentes.