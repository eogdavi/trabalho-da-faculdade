# trabalho-da-faculdade
Algoritmo e Pensamento Computacional
/*
 * Atividade 02 - Cifra de Cesar com sequencias numericas
 * Disciplina: Algoritmos e Pensamento Computacional
 *
 * Camada 1: Cifra de Cesar (SHIFT fixo)
 * Camada 2: deslocamento dinamico por sequencia (PA, PG, Fibonacci, Primos)
 * Deslocamento total da letra i = SHIFT + sequencia[i]
 */
#include <stdio.h>
#include <string.h>
#include <ctype.h>
#include <time.h>

#define MAX_LETRAS 15

/* ---------- Sequencias numericas ---------- */
static int eh_primo(long n) {
    if (n < 2) return 0;
    for (long d = 2; d * d <= n; d++)
        if (n % d == 0) return 0;
    return 1;
}

/* Preenche seq[0..n-1]. a1 e razao so sao usados em PA e PG. */
void gerar_sequencia(int tipo, int n, long a1, long razao, long seq[]) {
    switch (tipo) {
    case 1: /* PA: an = a1 + (n-1)*r */
        for (int i = 0; i < n; i++) seq[i] = a1 + i * razao;
        break;
    case 2: /* PG: an = a1 * q^(n-1) (calculada termo a termo, com mod para evitar overflow) */
        seq[0] = a1;
        for (int i = 1; i < n; i++) seq[i] = (seq[i - 1] * razao) % 26;
        break;
    case 3: /* Fibonacci: 1, 1, 2, 3, 5, 8, 13... */
        for (int i = 0; i < n; i++)
            seq[i] = (i < 2) ? 1 : (seq[i - 1] + seq[i - 2]) % 26;
        break;
    case 4: { /* Primos: 2, 3, 5, 7, 11... */
        long p = 2;
        for (int i = 0; i < n; ) {
            if (eh_primo(p)) seq[i++] = p;
            p++;
        }
        break;
    }
    }
}

const char *nome_sequencia(int tipo) {
    switch (tipo) {
    case 1: return "PA";
    case 2: return "PG";
    case 3: return "Fibonacci";
    case 4: return "Primos";
    }
    return "?";
}

/* ---------- Criptografia ---------- */
/* sentido = +1 criptografa, -1 descriptografa */
void processar(const char *entrada, char *saida, int shift, const long seq[], int sentido) {
    int n = (int)strlen(entrada);
    for (int i = 0; i < n; i++) {
        int desloc = (int)((shift + seq[i]) % 26);
        int pos = entrada[i] - 'a';
        pos = ((pos + sentido * desloc) % 26 + 26) % 26;
        saida[i] = (char)('a' + pos);
    }
    saida[n] = '\0';
}

/* ---------- Entrada e log ---------- */
int palavra_valida(const char *p) {
    int n = (int)strlen(p);
    if (n == 0 || n > MAX_LETRAS) return 0;
    for (int i = 0; i < n; i++)
        if (!isalpha((unsigned char)p[i])) return 0;
    return 1;
}

void registrar_log(const char *msg) {
    FILE *f = fopen("log_execucao.txt", "a");
    if (!f) return;
    time_t t = time(NULL);
    char hora[32];
    strftime(hora, sizeof hora, "%Y-%m-%d %H:%M:%S", localtime(&t));
    fprintf(f, "[%s] %s\n", hora, msg);
    fclose(f);
}

int main(void) {
    char palavra[64], resultado[64], msg[256];
    int opcao, shift, tipo;
    long a1 = 1, razao = 1, seq[MAX_LETRAS];

    do {
        printf("\n=== CIFRA DE CESAR + SEQUENCIAS ===\n");
        printf("1 - Criptografar\n2 - Descriptografar\n0 - Sair\nOpcao: ");
        if (scanf("%d", &opcao) != 1) break;
        if (opcao == 0) break;
        if (opcao != 1 && opcao != 2) { printf("Opcao invalida.\n"); continue; }

        printf("Palavra (ate %d letras, sem acentos): ", MAX_LETRAS);
        scanf("%63s", palavra);
        if (!palavra_valida(palavra)) {
            printf("Palavra invalida.\n");
            registrar_log("ERRO: palavra invalida");
            continue;
        }
        for (int i = 0; palavra[i]; i++) palavra[i] = (char)tolower((unsigned char)palavra[i]);

        printf("SHIFT: ");
        scanf("%d", &shift);

        printf("Sequencia: 1-PA  2-PG  3-Fibonacci  4-Primos\nTipo: ");
        scanf("%d", &tipo);
        if (tipo < 1 || tipo > 4) { printf("Tipo invalido.\n"); continue; }

        if (tipo == 1) {
            printf("Primeiro termo (a1) e razao (r): ");
            scanf("%ld %ld", &a1, &razao);
        } else if (tipo == 2) {
            prin…
