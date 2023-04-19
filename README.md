# Notas de Aula - Shell Scripting parte 1: scripts de automação de tarefas

# Link do Curso

[Curso Online Shell Scripting parte 1: scripts de automação de tarefas | Alura](https://cursos.alura.com.br/course/shellscripting)

# Aulas

## Montando um script para converter imagens

**Nome do arquivo:** 

conversao-jpg-png.sh

**Interpretador:**

 #!/bin/bash

**Para executar o arquivo *conversao-jpg-png.sh:*** 

bash conversao-jpg-png.sh< nome dos arquivos separados por espaço simples>

```bash
#!/bin/bash
CAMINHO_IMAGENS ~/Downloads/imagens-livros

for imagem in $@
do
    convert $CAMINHO_IMAGENS/$imagem.jpg $CAMINHO_IMAGENS/$imagem.png
done
```

## Realizando a conversão em um diretório

> A opção **-F** é usado quando queremos especificar um campo delimitador do parâmetro de texto passado. Nesse nosso exemplo, estamos "cortando" o parâmetro de texto passado onde tivermos o **.**
> 

Verificar a existência de uma pasta chamada ‘png’ e caso não exista, criá-la

```bash
if [ ! -d png ]
then
	mkdir png
fi
```

- Usamos o termo ‘local’ antes de uma variável para explicitá-la como uma variável local
- Se o status de saída de uma função for zero, significa que ela foi executada com sucesso
    - para verificar o status de saída
    
    ```bash
    if [ $? -eq 0]
    then
    	echo "conversao realizada com sucesso"
    else
    	echo "Houve uma falha no processo"
    fi
    ```
    

Redirecionando a mensagem de erro:

> Os descritores de arquivos são indicadores utilizados para acessar um arquivo ou fluxos padrões como entrada, saída, e mensagens de erros. Esses fluxos padrões de entrada, saída e mensagens de erro podem ser acessados pelos descritores 0,1 e 2 respectivamente.
> 
> 
> ```bash
> converte_imagem 2>erros_conversao.txt
> ```
> 

### Script da aula finalizado

```bash
#!/bin/bash

cd ~/Downloads/imagens-livros
if [ ! -d png ]
then
	mkdir png
fi

converte_imagem(){
for imagem in *.jpg
do
	local imagem_sem_extensao=$(ls $imagem | awk -F. '{ print $1 }')
  convert $imagem_sem_extensao.jpg png/$imagem_sem_extensao.png
done
}
converte_imagem 2>erros_conversao.txt
if [ $? -eq 0]
then
	echo "conversao realizada com sucesso"
else
	echo "Houve uma falha no processo"
fi
```

## Conversão de arquivos em diferentes diretórios

- uso de recursividade

```bash
#!/bin/bash

converte_imagem(){
    local caminho_imagem=$1
    local imagem_sem_extensao=$(ls $caminho_imagem | awk -F. '{ print $1 }')
    convert $imagem_sem_extensao.jpg $imagem_sem_extensao.png
}

varrer_diretorio(){
    cd $1
    for arquivo in *
    do
        local caminho_arquivo=$(find ~/Downloads/imagens-novos-livros -name $arquivo)
        if [ -d $caminho_arquivo ]
        then
            varrer_diretorio $caminho_arquivo
        else
            converte_imagem $caminho_arquivo
        fi
    done
}

varrer_diretorio ~/Downloads/imagens-novos-livros

if [ $? -eq 0 ]
then
    echo "Conversão realizada com sucesso"
else
    echo "Houve um problema na conversão"
fi
```

## Nomes dos processos

bash processos-memoria-alocada .sh

```bash
#!/bin/bash

processos=$(ps -e -o pid --sort -size|head -n 11 | grep [0-9])
for pid in $processos
do
	echo$(ps-p $pid -o comm=)
done
```

## Salvando processos em arquivos separados

- para fazer cálculos matemáticos usa-se bc <<<
    - exemplo
    
    ```bash
    bc <<< 7/5 # resultado inteiro
    bc <<< "scale=2;7/5" # resultado com duas casas decimais
    ```
    

bash processos-memoria .sh

```bash
#!/bin/bash

if [ ! -d log ]
then
	mkdir png
fi

processos_memoria(){
	processos=$(ps -e -o pid --sort -size|head -n 11 | grep [0-9])
	for pid in $processos
	do
		nome_processo=$(ps -p $pid -o comm=)
		echo $(date +%F, %H:%M:%S) >> $nome_processo.log
		tamanho_processo=$(ps -p $pid -o size | grep [0-9])
		echo "$(bc <<< "scale=2;$tamanho_processo/1024") MB" >> log/$nome_processo.log	  
	done
}

processos_memoria
if [ $? -eq 0 ]
then
    echo "Os arquivos foram salvos com sucesso"
else
    echo "Houve um problema na hora de salvar os arquivos"
fi
```
