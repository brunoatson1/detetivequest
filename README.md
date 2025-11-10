#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

typedef struct Sala {
    char nome[64];
    struct Sala* esquerda;
    struct Sala* direita;
} Sala;

// criarSala() – cria, de forma dinâmica, uma sala com nome.
Sala* criarSala(const char* nome, Sala* esq, Sala* dir) {
    Sala* s = (Sala*)malloc(sizeof(Sala));
    if (!s) {
        fprintf(stderr, "Erro: sem memoria para criar sala '%s'\n", nome);
        exit(EXIT_FAILURE);
    }
    strncpy(s->nome, nome, sizeof(s->nome) - 1);
    s->nome[sizeof(s->nome) - 1] = '\0';
    s->esquerda = esq;
    s->direita  = dir;
    return s;
}

void liberarArvore(Sala* raiz) {
    if (!raiz) return;
    liberarArvore(raiz->esquerda);
    liberarArvore(raiz->direita);
    free(raiz);
}

// explorarSalas() – permite a navegação do jogador pela árvore.
void explorarSalas(Sala* raiz) {
    if (!raiz) return;

    Sala* atual = raiz;
    char linha[32];

    printf("Bem-vindo(a) ao Detective Quest!\n");
    printf("Voce comeca no: %s\n", atual->nome);
    printf("Comandos: e = esquerda, d = direita, s = sair\n\n");

    while (1) {
        printf("Sala atual: %s\n", atual->nome);

        // Nó-folha: sem caminhos
        if (!atual->esquerda && !atual->direita) {
            printf("Fim do caminho! Voce chegou a um comodo sem saidas.\n");
            break;
        }

        if (atual->esquerda)  printf(" - Caminho a esquerda: %s\n", atual->esquerda->nome);
        if (atual->direita)   printf(" - Caminho a direita:  %s\n", atual->direita->nome);

        printf("Escolha (e/d/s): ");
        if (!fgets(linha, sizeof(linha), stdin)) break;
        char op = (char)tolower((unsigned char)linha[0]);
        if (op == 's') {
            printf("Exploracao encerrada pelo jogador.\n");
            break;
        } else if (op == 'e') {
            if (atual->esquerda) {
                atual = atual->esquerda;
            } else {
                printf("Nao ha caminho a esquerda. Tente outra direcao.\n");
            }
        } else if (op == 'd') {
            if (atual->direita) {
                atual = atual->direita;
            } else {
                printf("Nao ha caminho a direita. Tente outra direcao.\n");
            }
        } else {
            printf("Opcao invalida. Use 'e', 'd' ou 's'.\n");
        }

        printf("\n");
    }
}

// main() – monta o mapa inicial e da inicio a exploracao.
int main(void) {
    /*
      Exemplo de mapa (fixo):
                  Hall
               /        \
          Sala de Estar   Cozinha
            /     \          \
         Biblioteca  Jardim   Despensa

      Você pode ajustar nomes/estrutura conforme preferir, mantendo binária.
    */

    Sala* biblioteca = criarSala("Biblioteca", NULL, NULL);
    Sala* jardim     = criarSala("Jardim", NULL, NULL);
    Sala* despensa   = criarSala("Despensa", NULL, NULL);

    Sala* salaEstar  = criarSala("Sala de Estar", biblioteca, jardim);
    Sala* cozinha    = criarSala("Cozinha", NULL, despensa);

    Sala* hall       = criarSala("Hall", salaEstar, cozinha);

    explorarSalas(hall);
    liberarArvore(hall);
    return 0;
}
