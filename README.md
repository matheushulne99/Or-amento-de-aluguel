Projeto Acadêmico (1º Semestre): Sistema de automação de orçamentos mensais e anuais para a Imobiliária R.M, desenvolvido em Python com foco em lógica de programação e persistência de dados (CSV).


# =====================================================
# ORÇAMENTO DE ALUGUEL - R.M IMOBILIÁRIA
# Aplicação desenvolvida para a disciplina de Programação
# =====================================================

import csv                      # Biblioteca para trabalhar com arquivos CSV
from datetime import datetime   # Para gerar nome de arquivo com data e hora


class Imovel:
    """
    Classe responsável por representar um imóvel e calcular seu valor de aluguel.
    Utiliza programação orientada a objetos (POO) como solicitado.
    """
    
    def __init__(self, tipo, quartos=1, garagem=False, tem_criancas=True, vagas_extras=0):
        """
        Construtor da classe Imovel.
        Recebe todas as informações necessárias para calcular o valor.
        """
        self.tipo = tipo.lower()           # 'apartamento', 'casa' ou 'estudio'
        self.quartos = quartos
        self.garagem = garagem
        self.tem_criancas = tem_criancas
        self.vagas_extras = vagas_extras
        self.valor_mensal = 0.0            # Será calculado depois


    def calcular_valor_mensal(self):
        """
        Calcula o valor mensal do aluguel conforme as regras da R.M Imobiliária.
        """
        if self.tipo == "apartamento":
            self.valor_mensal = 700.00                     # Valor base
            
            if self.quartos >= 2:                          # Regra c)
                self.valor_mensal += 200.00
                
            if self.garagem:                               # Regra e)
                self.valor_mensal += 300.00
                
            if not self.tem_criancas:                      # Regra g) - Desconto de 5%
                self.valor_mensal *= 0.95

        elif self.tipo == "casa":
            self.valor_mensal = 900.00                     # Valor base
            
            if self.quartos >= 2:                          # Regra d)
                self.valor_mensal += 250.00
                
            if self.garagem:                               # Regra e)
                self.valor_mensal += 300.00

        elif self.tipo == "estudio":
            self.valor_mensal = 1200.00                    # Valor base
            
            if self.garagem:                               # Regra f)
                self.valor_mensal += 250.00                # 2 vagas inclusas
                self.valor_mensal += self.vagas_extras * 60.00  # Vagas extras

        return round(self.valor_mensal, 2)                 # Arredonda para 2 casas decimais


def main():
    """
    Função principal do programa.
    Responsável por interagir com o usuário e orquestrar o fluxo.
    """
    print("="*65)
    print("        ORÇAMENTO DE ALUGUEL - R.M IMOBILIÁRIA")
    print("="*65)

    # =================== ENTRADA DE DADOS ===================
    
    # Validação do tipo de imóvel
    tipo = input("\nTipo de imóvel (Apartamento / Casa / Estudio): ").strip().lower()
    while tipo not in ["apartamento", "casa", "estudio"]:
        tipo = input("Tipo inválido! Digite novamente: ").strip().lower()

    # Quartos (apenas para apartamento e casa)
    quartos = 1
    if tipo in ["apartamento", "casa"]:
        quartos = int(input(f"Quantidade de quartos (1 ou 2): "))
        while quartos not in [1, 2]:
            quartos = int(input("Digite apenas 1 ou 2: "))

    # Garagem
    garagem = input("Deseja vaga de garagem? (S/N): ").strip().upper() == "S"

    # Vagas extras (apenas para estúdio)
    vagas_extras = 0
    if tipo == "estudio" and garagem:
        vagas_extras = int(input("Quantas vagas EXTRAS além das 2 inclusas? (0 ou mais): "))

    # Crianças (apenas para apartamento)
    tem_criancas = True
    if tipo == "apartamento":
        tem_criancas = input("Possui crianças morando? (S/N): ").strip().upper() == "S"

    # =================== CÁLCULO ===================
    
    imovel = Imovel(tipo, quartos, garagem, tem_criancas, vagas_extras)
    valor_mensal = imovel.calcular_valor_mensal()
    valor_contrato = 2000.00

    # =================== EXIBIÇÃO DO ORÇAMENTO ===================
    
    print("\n" + "="*65)
    print("                  ORÇAMENTO GERADO")
    print("="*65)
    print(f"Tipo de Imóvel      : {tipo.capitalize()}")
    print(f"Quartos             : {quartos}")
    print(f"Garagem             : {'Sim' if garagem else 'Não'}")
    
    if tipo == "estudio" and garagem:
        print(f"Vagas (2 + extras)  : 2 + {vagas_extras}")
        
    if tipo == "apartamento":
        print(f"Possui crianças     : {'Sim' if tem_criancas else 'Não'}")
        
    print(f"Valor Mensal        : R$ {valor_mensal:.2f}")
    print(f"Valor do Contrato   : R$ {valor_contrato:.2f} (pode parcelar em até 5x)")
    print("="*65)

    # =================== GERAÇÃO DO ARQUIVO CSV ===================
    
    gerar = input("\nDeseja gerar o arquivo CSV com as 12 parcelas? (S/N): ").strip().upper()
    if gerar == "S":
        nome_arquivo = f"orcamento_rm_{datetime.now().strftime('%Y%m%d_%H%M')}.csv"
        
        with open(nome_arquivo, 'w', newline='', encoding='utf-8') as f:
            writer = csv.writer(f)
            writer.writerow(["Mês", "Aluguel Mensal", "Parcela Contrato", "Total do Mês"])
            
            parcela_contrato = round(valor_contrato / 5, 2)   # Divide em 5x
            
            for mes in range(1, 13):
                valor_contrato_mes = parcela_contrato if mes <= 5 else 0.00
                total_mes = valor_mensal + valor_contrato_mes
                
                writer.writerow([
                    f"Mês {mes}",
                    f"R$ {valor_mensal:.2f}",
                    f"R$ {valor_contrato_mes:.2f}",
                    f"R$ {total_mes:.2f}"
                ])
        
        print(f"\n✅ Arquivo gerado com sucesso: {nome_arquivo}")


# Execução do programa
if __name__ == "__main__":
    main()
