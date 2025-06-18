#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define TAM 8
#define NAVIOS 3

// Funções
void inicializarTabuleiro(char tabuleiro[TAM][TAM], char simbolo) {
    for (int i = 0; i < TAM; i++)
        for (int j = 0; j < TAM; j++)
            tabuleiro[i][j] = simbolo;
}

void mostrarTabuleiro(char tabuleiro[TAM][TAM], int ocultarNavios) {
    printf("  A B C D E F G H\n");
    for (int i = 0; i < TAM; i++) {
        printf("%d ", i + 1);
        for (int j = 0; j < TAM; j++) {
            char c = tabuleiro[i][j];
            if (ocultarNavios && c == 'N') // ocultar navios do oponente
                printf("~ ");
            else
                printf("%c ", c);
        }
        printf("\n");
    }
}

void posicionarNavios(char tabuleiro[TAM][TAM]) {
    int i = 0;
    while (i < NAVIOS) {
        int x = rand() % TAM;
        int y = rand() % TAM;
        if (tabuleiro[x][y] == '~') {
            tabuleiro[x][y] = 'N';
            i++;
        }
    }
}

int atacar(char tabuleiro[TAM][TAM], int x, int y) {
    if (tabuleiro[x][y] == 'N') {
        tabuleiro[x][y] = 'X'; // acertou
        return 1;
    } else if (tabuleiro[x][y] == '~') {
        tabuleiro[x][y] = 'O'; // errou
        return 0;
    }
    return -1; // já atirado antes
}

int contarNaviosRestantes(char tabuleiro[TAM][TAM]) {
    int count = 0;
    for (int i = 0; i < TAM; i++)
        for (int j = 0; j < TAM; j++)
            if (tabuleiro[i][j] == 'N')
                count++;
    return count;
}

// Converte entrada tipo "A5" em coordenadas
int converterCoordenada(char letra) {
    if (letra >= 'A' && letra <= 'H') return letra - 'A';
    if (letra >= 'a' && letra <= 'h') return letra - 'a';
    return -1;
}

int main() {
    char tabuleiroJogador[TAM][TAM];
    char tabuleiroComputador[TAM][TAM];
    char tiro[3];
    int x, y, acerto, turno = 1;

    srand(time(NULL));
    
    inicializarTabuleiro(tabuleiroJogador, '~');
    inicializarTabuleiro(tabuleiroComputador, '~');

    posicionarNavios(tabuleiroJogador);
    posicionarNavios(tabuleiroComputador);

    printf("🚢 Bem-vindo à BATALHA NAVAL! 🚢\n");
    printf("Você tem %d navios. Acerte todos os do inimigo!\n\n", NAVIOS);

    while (1) {
        printf("\n--- SEU TABULEIRO ---\n");
        mostrarTabuleiro(tabuleiroJogador, 0);
        printf("\n--- TABULEIRO DO COMPUTADOR ---\n");
        mostrarTabuleiro(tabuleiroComputador, 1);

        if (turno % 2 == 1) {
            // Jogador ataca
            printf("\n🧭 Sua vez! Digite coordenada (ex: A5): ");
            scanf("%s", tiro);
            y = converterCoordenada(tiro[0]);
            x = tiro[1] - '1';

            if (x < 0 || x >= TAM || y < 0 || y >= TAM) {
                printf("Coordenada inválida! Tente de novo.\n");
                continue;
            }

            acerto = atacar(tabuleiroComputador, x, y);
            if (acerto == 1)
                printf("💥 ACERTOU um navio inimigo!\n");
            else if (acerto == 0)
                printf("🌊 Errou. Só água por aqui...\n");
            else {
                printf("⛔ Já atacou aqui! Tente outro lugar.\n");
                continue;
            }
        } else {
            // Computador ataca
            do {
                x = rand() % TAM;
                y = rand() % TAM;
                acerto = atacar(tabuleiroJogador, x, y);
            } while (acerto == -1);

            printf("\n💻 Computador atacou em %c%d... ", y + 'A', x + 1);
            if (acerto == 1)
                printf("Acertou seu navio!\n");
            else
                printf("Errou!\n");
        }

        // Verificação de fim de jogo
        if (contarNaviosRestantes(tabuleiroJogador) == 0) {
            printf("\n💻 O computador afundou todos os seus navios. Você perdeu!\n");
            break;
        }

        if (contarNaviosRestantes(tabuleiroComputador) == 0) {
            printf("\n🎉 Parabéns! Você afundou todos os navios do computador!\n");
            break;
        }

        turno++;
    }

    return 0;
}
