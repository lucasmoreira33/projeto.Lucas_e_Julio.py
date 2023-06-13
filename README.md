# projeto.Lucas_e_Julio.py

import openai

vendedores = {}
produtos = {}
clientes = {}

def cadastrar_vendedor():
    nome = input('Digite o nome do vendedor: ')
    email = input('Digite o email do vendedor: ')
    while '@gmail.com' not in email and '@hotmail.com' not in email:
        print('Digite um email com as informações corretas contendo um @gmail.com ou @hotmail.com, por gentileza')
        email = input('Digite um email com as informações corretas: ')

    if email in vendedores:
        print('Vendedor já cadastrado.')
    else:
        login = input('Digite o login do vendedor: ')
        senha = input('Digite a senha do vendedor: ')

        vendedores[email] = {
            'nome': nome,
            'login': login,
            'senha': senha,
            'produtos': []
        }

        print('Vendedor cadastrado com sucesso!')

def fazer_login_vendedor():
    email = input('Digite o email do vendedor: ')
    senha = input('Digite a senha do vendedor: ')

    if email in vendedores and vendedores[email]['senha'] == senha:
        print('Login realizado com sucesso!')
        menu_vendedor(email)
    else:
        print('Email ou senha incorretos.')

def atualizar_senha_vendedor(email):
    senha = input('Digite a nova senha: ')
    vendedores[email]['senha'] = senha
    print('Senha atualizada com sucesso!')

def adicionar_produto(email):
    nome_produto = input('Digite o nome do produto: ')
    preco = float(input('Digite o preço do produto: '))
    quantidade = int(input('Digite a quantidade do produto: '))

    produto = {
        'nome': nome_produto,
        'preco': preco,
        'quantidade': quantidade
    }

    produtos[nome_produto] = produto

    vendedores[email]['produtos'].append(nome_produto)

    print('Produto adicionado com sucesso!')

def adicionar_produto_validado(email):
    nome_produto = input('Digite o nome do produto: ')

    preco = input('Digite o preço do produto: ')
    preco = float(preco.replace(',', '.'))
    quantidade = int(input('Digite a quantidade do produto: '))

    produto = {
        'nome': nome_produto,

        'preco': preco,
        'quantidade': quantidade
    }

    produtos[nome_produto] = produto

    vendedores[email]['produtos'].append(nome_produto)

    print('Produto adicionado com sucesso!')

def atualizar_produto():
    nome_produto = input('Digite o nome do produto que deseja atualizar: ')

    if nome_produto in produtos:
        novo_preco = float(input('Digite o novo preço do produto: '))
        nova_quantidade = int(input('Digite a nova quantidade do produto: '))

        produtos[nome_produto]['preco'] = novo_preco
        produtos[nome_produto]['quantidade'] = nova_quantidade

        print(f"Produto '{nome_produto}' atualizado com sucesso!")
    else:
        print('Produto não encontrado.')

def remover_produto(email):
    nome_produto = input('Digite o nome do produto: ')

    if nome_produto in produtos:
        del produtos[nome_produto]
        vendedores[email]['produtos'].remove(nome_produto)
        print('Produto removido com sucesso!')
    else:
        print('Produto não encontrado.')

def listar_produtos(email):
    print('Produtos do vendedor:')

    for produto_nome in vendedores[email]['produtos']:
        produto = produtos.get(produto_nome)
        if produto:
            print(f"Nome: {produto['nome']}")
            print(f"Preço: R${produto['preco']}")
            print(f"Quantidade: {produto['quantidade']}")
            print('------------------------')
        else:
            print(f"Produto '{produto_nome}' não encontrado.")

def consultarchatgpt(produto):
    openai.api_key = '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'

    model_engine = "text-davinci-003"
    prompt = 'me diga resumidamente o que você acha do ' + produto + ' ?'
    max_tokens = 1024

    completion = openai.Completion.create(
        engine=model_engine,
        prompt=prompt,
        max_tokens=max_tokens,
        temperature=0.5,
        top_p=1,
        frequency_penalty=0,
        presence_penalty=0
    )

    return completion.choices[0].text

def menu_vendedor(email):
    while True:
        print('\n===== MENU VENDEDOR =====')
        print('1 - Adicionar produto')
        print('2 - Remover produto')
        print('3 - Listar produtos')
        print('4 - Atualizar produtos')
        print('5 - Atualizar senha')
        print('0 - Voltar ao menu principal')

        opcao = input('Escolha uma opção: ')

        if opcao == '1':
            adicionar_produto_validado(email)
        elif opcao == '2':
            remover_produto(email)
        elif opcao == '3':
            listar_produtos(email)
        elif opcao == '4':
            atualizar_produto()
        elif opcao == '5':
            atualizar_senha_vendedor(email)
        elif opcao == '0':
            print('Voltando ao menu principal')
            break
        else:
            print('Opção inválida!')

def menu_cliente():
    while True:
        print('\n===== MENU CLIENTE =====')
        print('1 - Buscar produtos')
        print('2 - Comprar produtos')
        print('3 - Listar compras ')
        print('4 - Consultar produto ')
        print('0 - Voltar ao menu principal')

        opcao = input('Escolha uma opção:')

        if opcao == '1':
            buscar_produtos()
        elif opcao == '2':
            comprar_produto()
        elif opcao == '3':
            listar_compras_usuario()
        elif opcao == '4':
            buscar_produtos_usando_chatGPT()
        elif opcao == '0':
            print('Voltando ao menu principal')
            break
        else:
            print('Opção inválida!')

def buscar_produtos():
    termo_busca = input('Digite o termo de busca: ')

    resultados = []
    for produto in produtos.values():
        if termo_busca.lower() in produto['nome'].lower():
            resultados.append(produto)

    if resultados:
        print('Produtos encontrados:')
        for produto in resultados:
            print(f"Nome: {produto['nome']}")
            print(f"Preço: R${produto['preco']}")
            print(f"Quantidade: {produto['quantidade']}")
            print('------------------------')
    else:
        print('Nenhum produto encontrado com o termo de busca informado.')

def comprar_produto():
    email_vendedor = input('Digite o email do vendedor: ')
    nome_produto = input('Digite o nome do produto que deseja comprar: ')

    if email_vendedor in vendedores:
        vendedor = vendedores[email_vendedor]
        if nome_produto in vendedor['produtos']:
            produto = produtos[nome_produto]

            quantidade_disponivel = produto['quantidade']
            preco_unitario = produto['preco']

            quantidade_desejada = int(input('Digite a quantidade que deseja comprar: '))

            if quantidade_desejada <= quantidade_disponivel:
                valor_total = preco_unitario * quantidade_desejada
                print(f'Valor total da compra: R${valor_total}')

                produto['quantidade'] -= quantidade_desejada

                nome_cliente = input('Digite o nome do cliente: ')
                if nome_cliente in clientes:
                    if 'compras' in clientes[nome_cliente]:
                        clientes[nome_cliente]['compras'].append({
                            'vendedor': email_vendedor,
                            'produto': nome_produto,
                            'quantidade': quantidade_desejada,
                            'valor_total': valor_total
                        })
                    else:
                        clientes[nome_cliente]['compras'] = [{
                            'vendedor': email_vendedor,
                            'produto': nome_produto,
                            'quantidade': quantidade_desejada,
                            'valor_total': valor_total
                        }]
                else:
                    clientes[nome_cliente] = {
                        'compras': [{
                            'vendedor': email_vendedor,
                            'produto': nome_produto,
                            'quantidade': quantidade_desejada,
                            'valor_total': valor_total
                        }]
                    }

                print('Compra realizada com sucesso!')
            else:
                print('Quantidade indisponível para compra.')
        else:
            print('Produto não encontrado.')
    else:
        print('Vendedor não encontrado.')

def listar_compras_usuario():
    nome_cliente = input('Digite o nome do cliente: ')

    if nome_cliente in clientes:
        if 'compras' in clientes[nome_cliente]:
            compras = clientes[nome_cliente]['compras']
            if compras:
                print(f'Compras realizadas por {nome_cliente}:')
                for compra in compras:
                    email_vendedor = compra['vendedor']
                    nome_produto = compra['produto']
                    quantidade = compra['quantidade']
                    valor_total = compra['valor_total']

                    vendedor = vendedores.get(email_vendedor)
                    produto = produtos.get(nome_produto)

                    if vendedor and produto:
                        print("=== Detalhes da Compra ===")
                        print(f"Vendedor: {vendedor['nome']}")
                        print(f"Produto: {produto['nome']}")
                        print(f'Quantidade: {quantidade}')
                        print(f'Valor Total: R${valor_total}')
                        print('---------------------------')
            else:
                print(f'O cliente {nome_cliente} não fez compras.')
        else:
            print(f'O cliente {nome_cliente} não fez compras.')
    else:
        print(f'Cliente {nome_cliente} não encontrado.')

def buscar_produtos_usando_chatGPT():
    termo_busca = input('Digite o termo de busca: ')

    resultados = []
    for produto in produtos.values():
        if termo_busca.lower() in produto['nome'].lower():
            resultados.append(produto)

    if resultados:
        print('Produtos encontrados:')
        for produto in resultados:
            print(f"Nome: {produto['nome']}")
            print(f"Preço: R${produto['preco']}")
            print(f"Quantidade: {produto['quantidade']}")
            print(f"Descricao vinda do ChatGPT: {consultarchatgpt(produto['nome'])}")
            print('------------------------')
    else:
        print('Nenhum produto encontrado com o termo de busca informado.')

def menu_principal():
    while True:
        print('\n===== MENU PRINCIPAL =====')
        print('1 - Cadastro de vendedor')
        print('2 - Login de vendedor')
        print('3 - Menu do cliente')
        print('0 - Sair do sistema')

        opcao = input('Escolha uma opção: ')

        if opcao == '1':
            cadastrar_vendedor()
        elif opcao == '2':
            fazer_login_vendedor()
        elif opcao == '3':
            menu_cliente()
        elif opcao == '0':
            print('Saindo do sistema')
            break
        else:
            print('Opção inválida!')

menu_principal()
