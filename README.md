#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define MAX_VIDA 100
#define MAX_INIMIGOS 5

typedef struct {
    int vida;
    int municao;
    int x, y;
} Jogador;

typedef struct {
    int vida;
    int x, y;
} Inimigo;

void mover(Jogador *j) {
    int dir;
    printf("\nMover: 1-Cima 2-Baixo 3-Esquerda 4-Direita\nEscolha: ");
    scanf("%d", &dir);

    if (dir == 1) j->y--;
    else if (dir == 2) j->y++;
    else if (dir == 3) j->x--;
    else if (dir == 4) j->x++;
}

void atirar(Jogador *j, Inimigo inimigos[], int n) {
    if (j->municao <= 0) {
        printf("Sem munição!\n");
        return;
    }
    j->municao--;

    int i;
    for (i = 0; i < n; i++) {
        if (inimigos[i].vida > 0 && abs(j->x - inimigos[i].x) <= 1 && abs(j->y - inimigos[i].y) <= 1) {
            inimigos[i].vida -= 50;
            printf("Você atirou em um inimigo! Vida do inimigo: %d\n", inimigos[i].vida);
            return;
        }
    }
    printf("Nenhum inimigo por perto!\n");
}

void curar(Jogador *j) {
    j->vida += 20;
    if (j->vida > MAX_VIDA) j->vida = MAX_VIDA;
    printf("Você se curou. Vida: %d\n", j->vida);
}

int main() {
    srand(time(NULL));

    Jogador player = {MAX_VIDA, 10, 5, 5};
    Inimigo inimigos[MAX_INIMIGOS];

    for (int i = 0; i < MAX_INIMIGOS; i++) {
        inimigos[i].vida = 100;
        inimigos[i].x = rand() % 10;
        inimigos[i].y = rand() % 10;
    }

    int turno = 1;
    while (player.vida > 0) {
        printf("\n=== TURNO %d ===\n", turno++);
        printf("Jogador: Vida=%d | Munição=%d | Pos=(%d,%d)\n", player.vida, player.municao, player.x, player.y);

        int escolha;
        printf("1-Mover  2-Atirar  3-Curar\nEscolha: ");
        scanf("%d", &escolha);

        if (escolha == 1) mover(&player);
        else if (escolha == 2) atirar(&player, inimigos, MAX_INIMIGOS);
        else if (escolha == 3) curar(&player);

        // inimigos atacam
        for (int i = 0; i < MAX_INIMIGOS; i++) {
            if (inimigos[i].vida > 0 && abs(player.x - inimigos[i].x) <= 1 && abs(player.y - inimigos[i].y) <= 1) {
                player.vida -= 10;
                printf("Um inimigo atacou você! Vida: %d\n", player.vida);
            }
        }

        // verifica se ainda há inimigos
        int vivos = 0;
        for (int i = 0; i < MAX_INIMIGOS; i++) {
            if (inimigos[i].vida > 0) vivos++;
        }
        if (vivos == 0) {
            printf("\nParabéns! Você eliminou todos os inimigos e venceu a partida!\n");
            break;
        }
    }

    if (player.vida <= 0) {
        printf("\nGAME OVER! Você foi eliminado.\n");
    }

    return 0;
}
