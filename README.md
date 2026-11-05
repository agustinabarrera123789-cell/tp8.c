#include <stdio.h>
#include <stdlib.h>

#define DIA_MAX 12.0f
#define DIA_MIN 10.0f


int rectificable(float diametro, float max);
int rechazada(float diametro, float min);

void cargar_piezas(float *p, int n);
int contar(int (*criterio)(float, float), float valor, float *p, int n);
float* reserva_memoria(int n);
void segregar(int (*criterio)(float, float), float valor, float *p, int n, float *q);
void mostrar_piezas(char *mensaje, float *p, int n);

int main(void) {
	int n;
	float *piezas = NULL;
	float *rectificables = NULL;
	float *rechazadas = NULL;
	
	printf("Cuántas piezas ingresará? ");
	if (scanf("%d", &n) != 1 || n <= 0) {
		printf("Cantidad de piezas inválida.\n");
		return 1;
	}
	
	piezas = reserva_memoria(n);
	cargar_piezas(piezas, n);
	
	int n_rectificar = contar(rectificable, DIA_MAX, piezas, n);
	if (n_rectificar > 0) {
		rectificables = reserva_memoria(n_rectificar);
		segregar(rectificable, DIA_MAX, piezas, n, rectificables);
	}
	mostrar_piezas("Piezas rectificables:", rectificables, n_rectificar);
	
	int n_rechazadas = contar(rechazada, DIA_MIN, piezas, n);
	if (n_rechazadas > 0) {
		rechazadas = reserva_memoria(n_rechazadas);
		segregar(rechazada, DIA_MIN, piezas, n, rechazadas);
	}
	mostrar_piezas("Piezas rechazadas:", rechazadas, n_rechazadas);
	
	free(piezas);
	free(rectificables);
	free(rechazadas);
	
	return 0;
}


int rectificable(float diametro, float max) {
	return (diametro > max) ? 1 : 0;
}

int rechazada(float diametro, float min) {
	return (diametro < min) ? 1 : 0;
}


void cargar_piezas(float *p, int n) {
	for (int i = 0; i < n; i++) {
		float valor;
		do {
			printf("Ingrese diámetro de pieza %d: ", i);
			scanf("%f", &valor);
			if (valor <= 0.0f) {
				printf("Error: El diámetro debe ser mayor a cero. Intente nuevamente.\n");
			}
		} while (valor <= 0.0f);
		
		*(p + i) = valor;
	}
}


  int contar(int (*criterio)(float, float), float valor, float *p, int n) {
	int contador = 0;
	for (int i = 0; i < n; i++) {
		if (criterio(*(p + i), valor)) {
			contador++;
		}
	}
	return contador;
}


float* reserva_memoria(int n) {
	float *ptr = (float*) malloc((size_t)n * sizeof(float));
	if (ptr == NULL) {
		printf("Error: No se pudo asignar memoria suficiente.\n");
		exit(EXIT_FAILURE);
	}
	return 0;
}


void segregar(int (*criterio)(float, float), float valor, float *p, int n, float *q) {
	int j = 0;
	for (int i = 0; i < n; i++) {
		if (criterio(*(p + i), valor)) {
			*(q + j) = *(p + i);
			j++;
		}
	}
}


void mostrar_piezas(char *mensaje, float *p, int n) {
	printf("%s\n", mensaje);
	for (int i = 0; i < n; i++) {
		printf("Diámetro: %.2f\n", *(p + i));
	}
}
