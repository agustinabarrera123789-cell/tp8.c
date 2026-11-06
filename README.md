#include <stdio.h>
#include <stdlib.h>

#define MAX_DIAMETRO 12.0f
#define MIN_DIAMETRO 10.0f


void cargar_piezas(float *p, int n);
int contar(int (*criterio)(float, float), float valor, float *p, int n);
float* reserva_memoria(int n);
void segregar(int (*criterio)(float, float), float valor, float *p, int n, float *q);
void mostrar_piezas(char *mensaje, float *p, int n);
int rectificable(float diametro, float limite);
int rechazada(float diametro, float limite);



int rectificable(float diametro, float limite) {
	return (diametro > limite) ? 1 : 0;
}

int rechazada(float diametro, float limite) {
	return (diametro < limite) ? 1 : 0;
}

void cargar_piezas(float *p, int n) {
	int i;
	for (i = 0; i < n; i++) {
		do {
			printf("Ingrese diámetro de pieza %d: ", i);
			scanf("%f", (p + i));
			if (*(p + i) <= 0.0f) {
				printf("Error: El diámetro debe ser un valor positivo. Reintente.\n");
			}
		} while (*(p + i) <= 0.0f);
	}
}


int contar(int (*criterio)(float, float), float valor, float *p, int n) {
	int i;
	int contador = 0;
	for (i = 0; i < n; i++) {
		if (criterio(*(p + i), valor)) {
			contador++;
		}
	}
	return contador;
}


float* reserva_memoria(int n) {
	if (n <= 0) {
		return NULL;
	}
	float *ptr = (float *) malloc(n * sizeof(float));
	if (ptr == NULL) {
		printf("Error: No se pudo reservar memoria dinámica.\n");
		exit(1);
	}
	return ptr;
}


void segregar(int (*criterio)(float, float), float valor, float *p, int n, float *q) {
	int i;
	int j = 0;
	for (i = 0; i < n; i++) {
		if (criterio(*(p + i), valor)) {
			*(q + j) = *(p + i);
			j++;
		}
	}
}

void mostrar_piezas(char *mensaje, float *p, int n) {
	int i;
	printf("%s\n", mensaje);
	for (i = 0; i < n; i++) {
		printf("Diámetro: %.2f\n", *(p + i));
	}
}

int main(void) {
	int n;
	int n_rectificar = 0;
	int n_rechazadas = 0;
	
	printf("Cuántas piezas ingresará? ");
	if (scanf("%d", &n) != 1 || n <= 0) {
		printf("Cantidad no válida.\n");
		return 1;
	}
	
	float *piezas = reserva_memoria(n);
	
	cargar_piezas(piezas, n);
	
	
	n_rectificar = contar(rectificable, MAX_DIAMETRO, piezas, n);
	float *rectificables = reserva_memoria(n_rectificar);
	segregar(rectificable, MAX_DIAMETRO, piezas, n, rectificables);
	
	
	n_rechazadas = contar(rechazada, MIN_DIAMETRO, piezas, n);
	float *rechazadas = reserva_memoria(n_rechazadas);
	segregar(rechazada, MIN_DIAMETRO, piezas, n, rechazadas);
	
	mostrar_piezas("Piezas rectificables:", rectificables, n_rectificar);
	mostrar_piezas("Piezas rechazadas:", rechazadas, n_rechazadas);
	
	free(piezas);
	free(rectificables);
	free(rechazadas);
	
	return 0;
}
