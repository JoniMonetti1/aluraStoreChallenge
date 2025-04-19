# Análisis de Datos para Venta de Tienda

## 1. Propósito del análisis realizado

Este proyecto tiene como objetivo analizar los datos de las 4 tiendas pertenecientes a Juan para determinar cuál es la más adecuada para vender. El análisis se centra en cinco criterios fundamentales:

- **Análisis de facturación**: Comparativa del monto facturado por cada tienda (precio × cantidad de cuotas)
- **Ventas por categoría**: Evaluación del desempeño de diferentes categorías de productos en cada tienda
- **Calificación promedio**: Valoración de los clientes para cada establecimiento
- **Productos más y menos vendidos**: Identificación de productos destacados y de bajo rendimiento
- **Envío promedio**: Análisis de costos de envío asociados a cada tienda

Después de un análisis exhaustivo utilizando Python, pandas y diversas bibliotecas de visualización, se determinó que la **Tienda 4** es la mejor candidata para la venta.

## 2. Estructura del proyecto y organización de los archivos

```
proyecto-analisis-tiendas/
│
├── data/                         # URLs a los datasets usados
│   ├── tienda_1.csv              # Datos de la Tienda 1
│   ├── tienda_2.csv              # Datos de la Tienda 2 
│   ├── tienda_3.csv              # Datos de la Tienda 3
│   └── tienda_4.csv              # Datos de la Tienda 4
│
├── notebooks/                    # Jupyter notebooks con análisis completo
│   └── analisis_tiendas.ipynb    # Notebook principal con todo el análisis
│
├── outputs/                      # Resultados y visualizaciones generadas
│   ├── facturacion_tiendas.png   # Gráfico de facturación por tienda
│   ├── ventas_categorias.png     # Gráfico de ventas por categoría
│   ├── calificacion_tiendas.png  # Gráfico de calificación promedio
│   ├── productos_vendidos.png    # Gráfico de productos más/menos vendidos
│   └── costo_envio.png           # Gráfico de costo de envío promedio
│
├── requirements.txt             # Dependencias del proyecto
└── README.md                    # Este archivo
```

## 3. Ejemplos de gráficos e insights obtenidos

### Análisis de Facturación

```python
# Creamos una columna nueva llamada "facturacion"
df['facturacion'] = df['Precio'] * df['Cantidad de cuotas']

# Agrupamos por tienda y sumamos la facturación total
facturacion_total = df.groupby('tienda')['facturacion'].sum().sort_values()

# Configuramos pandas para mostrar los números sin notación científica
pd.options.display.float_format = '{:,.0f}'.format

# Mostramos la facturación total
print(facturacion_total)

# Identificamos la tienda con menor facturación
tienda_menor_facturacion = facturacion_total.idxmin()
print(f"La tienda con menor facturación es: {tienda_menor_facturacion}")

# Visualizamos la facturación
facturacion_total.plot(kind='bar', color='skyblue')
plt.title('Facturación Total por Tienda')
plt.ylabel('Monto facturado')
plt.xlabel('Tienda')
plt.xticks(rotation=0)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()
```

**Insight principal**: El análisis de facturación muestra diferencias significativas entre las tiendas, identificando la tienda con menor rendimiento financiero como candidata para ser vendida.

### Ventas por Categoría

```python
# Contamos cuántas veces se vendió cada categoría en cada tienda
categorias_populares = df.groupby(['tienda', 'Categoría del Producto'])['Producto'].count()

# Lo transformamos para encontrar la más popular por tienda
categorias_populares = categorias_populares.reset_index()
categorias_populares = categorias_populares.sort_values(['tienda', 'Producto'], ascending=[True, False])

# Nos quedamos con la categoría más popular por tienda
categorias_top = categorias_populares.groupby('tienda').first()

print(categorias_top)

# Graficamos con barras no apiladas
plt.figure(figsize=(12, 6))
sns.barplot(data=ventas_por_categoria, x='tienda', y='Producto', hue='Categoría del Producto')

# Añadimos etiquetas y título
plt.title('Ventas por categoría de producto y tienda')
plt.xlabel('Tienda')
plt.ylabel('Número de productos vendidos')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

**Insight principal**: El análisis por categorías revela qué tiendas tienen un desempeño deficiente en categorías clave o dependen demasiado de una sola categoría, lo que puede influir en la decisión de venta.

### Calificación Promedio

```python
# Calcular la calificación promedio por tienda
calificacion_promedio = df.groupby('tienda')['Calificación'].mean().sort_values(ascending=False)

# Mostrar los resultados
print(calificacion_promedio)

# Crear gráfico de barras
plt.figure(figsize=(10, 6))
sns.barplot(x=calificacion_promedio.index, y=calificacion_promedio.values, palette='viridis', hue=calificacion_promedio.index)

# Añadir título y etiquetas
plt.title('Calificación Promedio por Tienda')
plt.xlabel('Tienda')
plt.ylabel('Calificación Promedio')
plt.show()
```

**Insight principal**: Las calificaciones de los clientes son un indicador clave de la salud de la tienda y su potencial futuro. Las tiendas con calificaciones bajas podrían requerir intervención o ser candidatas para venta.

### Productos Más y Menos Vendidos

```python
# Productos más vendidos
productos_mas_vendidos = df.groupby(['tienda', 'Producto'])['Cantidad de cuotas'].sum().groupby('tienda').idxmax()
print("\nProductos más vendidos por tienda:\n")
for tienda, producto in productos_mas_vendidos.items():
    print(f"{tienda}              {producto}")

# Productos menos vendidos
productos_menos_vendidos = df.groupby(['tienda', 'Producto'])['Cantidad de cuotas'].sum().groupby('tienda').idxmin()
print("\nProductos menos vendidos por tienda:\n")
for tienda, producto in productos_menos_vendidos.items():
    print(f"{tienda}             {producto}")

# Analizar ventas totales
ventas_totales = df.groupby('tienda')['Cantidad de cuotas'].sum().sort_values()
print(ventas_totales)

tienda_menos_ventas = ventas_totales.idxmin()
print(f"La tienda que ha vendido menos productos en total es: {tienda_menos_ventas}")
```

**Insight principal**: Identificar los productos de mejor y peor desempeño por tienda ayuda a evaluar la eficiencia del inventario y estrategias de venta. La tienda con menor volumen total de ventas muestra señales de bajo rendimiento.

### Costo de Envío Promedio

```python
# Calcular el costo de envío promedio por tienda
envio_promedio = df.groupby('tienda')['Costo de envío'].mean()

# Mostrar el envío promedio por tienda
print(envio_promedio)

# Identificar la tienda con mayor costo de envío
tienda_mayor_costo_envio = costo_envio_promedio.idxmax()
print(f"La tienda con mayor costo de envío es: {tienda_mayor_costo_envio}")

# Gráfico de costo de envío promedio por tienda
plt.figure(figsize=(10,6))
sns.barplot(x=envio_promedio.index, y=envio_promedio.values, palette='viridis')
plt.title('Envío Promedio por Tienda')
plt.xlabel('Tienda')
plt.ylabel('Costo de Envío Promedio')
plt.show()
```

**Insight principal**: Los costos de envío afectan directamente la rentabilidad y competitividad. La tienda con mayores costos de envío promedio puede estar en desventaja competitiva, afectando su valoración de venta.

## 4. Instrucciones para ejecutar el notebook

### Requisitos previos
- Python 3.x
- Jupyter Notebook
- Bibliotecas: pandas, matplotlib, seaborn

### Pasos para ejecutar el análisis

1. Clone este repositorio:
```bash
git clone https://github.com/[usuario]/proyecto-analisis-tiendas.git
cd proyecto-analisis-tiendas
```

2. Instale las dependencias requeridas:
```bash
pip install pandas matplotlib seaborn jupyter
```

3. Inicie Jupyter Notebook:
```bash
jupyter notebook
```

4. Abra el notebook principal:
```
notebooks/analisis_tiendas.ipynb
```

5. Ejecute todas las celdas del notebook en orden (Menú -> Cell -> Run All)

### Fuentes de datos

El análisis utiliza datos de cuatro tiendas disponibles en GitHub:
```python
url = "https://raw.githubusercontent.com/alura-es-cursos/challenge1-data-science-latam/refs/heads/main/base-de-datos-challenge1-latam/tienda_1%20.csv"
url2 = "https://raw.githubusercontent.com/alura-es-cursos/challenge1-data-science-latam/refs/heads/main/base-de-datos-challenge1-latam/tienda_2.csv"
url3 = "https://raw.githubusercontent.com/alura-es-cursos/challenge1-data-science-latam/refs/heads/main/base-de-datos-challenge1-latam/tienda_3.csv"
url4 = "https://raw.githubusercontent.com/alura-es-cursos/challenge1-data-science-latam/refs/heads/main/base-de-datos-challenge1-latam/tienda_4.csv"
```

### Conclusión

Basado en el análisis completo de los cinco criterios evaluados (facturación, ventas por categoría, calificaciones, productos vendidos y costos de envío), se determinó que la **Tienda 4** es la más adecuada para vender. Esta conclusión se basa en su rendimiento comparativo en estos factores clave para el negocio.
