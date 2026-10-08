[Compilação de um clone novo](PUBLICATION.md) · [Licenças](THIRD_PARTY_NOTICES.md)

# Tab5FinalDoom — M5Stack Tab5

Port de **Final Doom: The Plutonia Experiment e TNT: Evilution**, derivado do
projeto local Tab5Doom (commit-base ed2331e). Engine doomgeneric, ESP32-P4,
display ST7123, teclado Tab5/USB e áudio ES8388 preservados da base.

## Arquivos no microSD

Use FAT32 e coloque os dois IWADs na mesma pasta:

```
/doom/plutonia.wad
/doom/tnt.wad
```

Também aceita PLUTONIA.WAD e TNT.WAD. Os arquivos não acompanham o projeto.
Esta versão não procura WADs na raiz do cartão nem na flash.

## Menu de campanha

Ao ligar, a tela mostra Plutonia e TNT e a disponibilidade de cada arquivo.
Use **setas para cima/baixo** ou **1/2** para selecionar; **Enter** inicia a
campanha. Funciona com o Tab5 Keyboard ou teclado USB. **R** repete a busca
(e tenta montar o cartão se a montagem inicial falhou). O menu verifica que
cada arquivo abre e possui cabeçalho IWAD; o engine valida os demais dados.
Uma opção ausente/inválida não pode iniciar. Mesmo com só um WAD presente,
o menu aguarda confirmação. Ao sair pelo menu do jogo, o port salva as
configurações e reinicia o Tab5 para retornar à seleção de campanha.
A iluminação é apagada antes do reinício e acesa após desenhar o primeiro
quadro do menu, para ocultar a transição durante a inicialização do painel.
Não há controle por touch nesta versão.

Saves e configurações são separados automaticamente:

```
/doom/plutonia/   # saves e default.cfg de Plutonia
/doom/tnt/        # saves e default.cfg de TNT
```

## Compilação

Use ESP-IDF **5.4.4**, alvo **esp32p4**:

```sh
idf.py build
```

O binário gerado é `build/Tab5FinalDoom.bin`. A imagem SPIFFS legada foi
retirada do build; os IWADs são lidos somente do microSD. Com a tabela de
partições compatível já instalada no Tab5:

```sh
idf.py -p <porta> app-flash monitor
```

Para instalar bootloader, tabela e aplicativo:

```sh
idf.py -p <porta> flash monitor
```

## Controles durante o jogo

- WASD ou setas: andar/girar.
- Ctrl: atirar. E ou Espaço: usar/abrir portas.
- Aa/Shift: correr. Alt + direção: deslocamento lateral.
- Enter: confirmar. Esc: menu. 1–7: armas.

Efeitos sonoros implementados; música ainda não implementada. A seleção e
as duas campanhas precisam de validação no aparelho. O histórico anterior
em TAB5DOOM_PROGRESS.txt e TAB5DOOM_DISPLAY_INIT_HISTORY.txt refere-se à base
Doom 1 e não demonstra testes de Final Doom.

## Validação local

Build ESP-IDF 5.4.4 concluído. Oito cenários do menu passaram usando seu código
real com stubs de plataforma, sem necessidade do Tab5:

```sh
python3 tests/test_menu.py
python3 tests/test_quit.py
```

Neste Mac, o SDK padrão 27 é incompatível com o linker instalado; os testes
foram executados com o SDK disponível 15.4:

```sh
SDKROOT=/Library/Developer/CommandLineTools/SDKs/MacOSX15.4.sdk python3 tests/test_menu.py
```

Os testes cobrem seleção, eventos de soltura de tecla, nomes maiúsculos,
arquivos ausentes/inválidos, nova tentativa de montagem, atualização da lista
e falha de alocação. Eles não validam o display físico nem as campanhas.
O teste de encerramento verifica que os callbacks terminam antes do reinício,
sem chamar exit() nem retornar ao loop do jogo. O reinício é simulado no teste;
a serial confirmou carga e saída das duas campanhas com o firmware corrigido,
reinicialização controlada e retorno ao menu, sem abort/panic. Captura:
`.debug_logs/final-doom-quit-validation.log`.

## Licença e créditos

Engine doomgeneric: GPL-2.0-or-later, id Software, Simon Howard e colaboradores,
ozkl e Alejandro Villegas Alonso (ESP32P4DOOM). Drivers e teclado mantêm seus
avisos em main/THIRD_PARTY_NOTICES.txt. Dados dos jogos são fornecidos pelo usuário.
