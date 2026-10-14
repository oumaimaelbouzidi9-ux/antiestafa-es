# AntiEstafa-ES

Proyecto del curso de Desarrollo de IA (Building AI)

##Resumen 

AntiEstafa-ES es una idea de IA que analiza mensajes recibidos por SMS, WhatsApp o correo en español, detecta si pueden ser una estafa (falsos bancos, falsos paquetes, premios inventados) y explica por qué resultan sospechosos. Esta IA la pensé sobretodo para personas mayores.

## Antecedentes

Cada vez recibimos más mensajes fraudulentos que se hacen pasar por bancos, empresas de mensajería o la Administración. Algunos son muy torpes, pero otros están bien hechos y cuesta darse cuenta, sobre todo si no estás acostumbrado a la tecnología.

Los problemas que quiero resolver son estos:

* Mucha gente, especialmente mayor, no sabe distinguir un mensaje falso de uno real.
* Los filtros de spam suelen estar pensados para inglés y fallan más con mensajes cortos en español (o en catalán).
* Las estafas cambian constantemente, así que las listas fijas de palabras sospechosas se quedan viejas enseguida.
* Cuando alguien duda, no tiene a mano una forma sencilla de preguntar "¿esto es una estafa?" y recibir una respuesta clara.

Elegí este problema porque es algo que nos puede pasar a cualquiera: con tener un móvil ya recibes estos mensajes. A veces dudo incluso yo de si uno es real o no, y pensé que quien tiene menos costumbre con la tecnología lo tiene todavía más difícil. Además, quería usar lo que estoy aprendiendo en el curso para algo útil y cercano, no para un problema inventado.

Me parece un tema importante porque una estafa no solo cuesta dinero: también deja a la persona asustada y con vergüenza, y muchas veces ni lo cuenta.

## ¿Cómo se utiliza?

Me gustaría que fuera lo más simple posible. La persona copia el mensaje sospechoso y lo pega en la aplicación, o lo comparte directamente desde el móvil. En pocos segundos recibe:

1. Un aviso tipo semáforo: **seguro**, **dudoso** o **probable estafa**.
2. Una explicación en lenguaje claro, por ejemplo "te mete prisa" o "te pide datos del banco".
3. Un consejo de qué hacer: no pulsar el enlace, llamar al teléfono oficial, borrar el mensaje o denunciarlo.

Lo podrían usar personas mayores, sus familiares, pequeños negocios, y también se podría enseñar en talleres de bibliotecas y centros cívicos.

Las personas afectadas son quienes reciben los mensajes, pero también las empresas a las que suplantan, que pierden la confianza de sus clientes. Algo que tengo claro es que la herramienta tiene que aconsejar, no decidir por nadie. Si se equivoca y marca como estafa un mensaje real, la persona debe poder comprobarlo por sí misma.

Aquí hay una demo pequeña para que se vea la idea:

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.pipeline import make_pipeline

# Mensajes de ejemplo inventados (en un proyecto real harían falta miles)
mensajes = [
    "Su paquete está retenido, pague 1,99 EUR en este enlace para liberarlo",
    "Banco: hemos bloqueado su cuenta, verifique sus datos ahora o perderá el acceso",
    "Has ganado un premio, introduce tu tarjeta para recibirlo hoy mismo",
    "Hacienda le debe una devolución, confirme su IBAN en el siguiente enlace",
    "Hola, ¿quedamos mañana a las 6 para tomar un café?",
    "Recuerda que la cita con el médico es el jueves a las 10:30",
    "Mamá, ya he llegado a casa, luego te llamo",
    "Tu pedido de la librería sale hoy y llegará el viernes",
]
etiquetas = [1, 1, 1, 1, 0, 0, 0, 0]  # 1 = estafa, 0 = mensaje normal

modelo = make_pipeline(TfidfVectorizer(lowercase=True), MultinomialNB())
modelo.fit(mensajes, etiquetas)

nuevo = ["Aviso: su cuenta será suspendida, verifique sus datos en este enlace"]
prob_estafa = modelo.predict_proba(nuevo)[0][1]
print(f"Probabilidad de estafa: {prob_estafa:.0%}")
```

## Datos y técnicas de IA

**Datos.** Para que funcione de verdad necesitaría muchos mensajes ya clasificados como estafa o como normales, en español y, más adelante, en catalán. Podrían salir de:

* Avisos publicados por organismos oficiales y cuerpos de seguridad, como el INCIBE, la Policía Nacional, los Mossos d'Esquadra o los propios bancos.
* Conjuntos de datos abiertos de spam y phishing, adaptados o traducidos.
* Mensajes que la gente quiera aportar voluntariamente, siempre anonimizados (sin nombres, teléfonos ni datos personales).

**Técnicas de IA.**

* Empezaría con clasificación de texto sencilla (TF-IDF y Naive Bayes o regresión logística), porque es rápida y fácil de entender y de explicar.
* Más adelante probaría un modelo de lenguaje preentrenado en español para que entienda mejor el contexto y detecte estafas nuevas.
* Me interesa que el sistema explique su decisión, mostrando qué ha pesado más: la urgencia, la petición de datos o un enlace raro.

| Elemento | Descripción |
|----------|-------------|
| Entrada | Texto del mensaje (y los enlaces que contenga) |
| Salida | Nivel de riesgo, motivo y consejo |
| Modelo inicial | TF-IDF + Naive Bayes |
| Modelo más avanzado | Modelo de lenguaje preentrenado en español |

## Desafíos

Esto es lo que el proyecto **no** resuelve:

* No evita que los estafadores envíen mensajes ni sustituye a la denuncia o a la prevención de bancos y operadoras. Solo ayuda a quien los recibe.
* Las estafas evolucionan, así que el modelo tendría que reentrenarse a menudo o perdería precisión.
* Puede equivocarse: marcar un mensaje legítimo como estafa (falso positivo) o dejar pasar uno peligroso (falso negativo). Por eso aconseja, no decide.
* La privacidad es delicada, porque los mensajes pueden contener datos personales. Habría que procesarlos en local siempre que se pueda, no guardarlos sin consentimiento y cumplir con el RGPD.
* Si los datos de entrenamiento son casi todos de un mismo tipo de español, funcionará peor con otras variantes del idioma.
* Existe el riesgo de que alguien confíe demasiado en el resultado, así que habría que dejar claro que no es infalible.

## ¿Qué sigue?

* Crear una aplicación sencilla (web o bot de Telegram o WhatsApp) para que cualquiera pueda usarla sin saber nada de tecnología.
* Probarla con personas reales, por ejemplo mayores y familiares, para ver si las explicaciones se entienden.
* Ampliar el soporte a catalán, gallego, euskera y al español de Latinoamérica.
* Analizar también enlaces, números de teléfono y remitentes.
* Para todo esto tendría que aprender más programación y necesitaría ayuda de gente de ciberseguridad, de diseño accesible y de alguien que entienda de protección de datos.

## Agradecimientos

* Inspirado en los avisos de seguridad del [INCIBE](https://www.incibe.es) y en los casos de estafas por SMS y WhatsApp que se ven cada vez más.
* La demo usa la biblioteca de código abierto [scikit-learn](https://scikit-learn.org) (licencia BSD).
* Plantilla y curso: Building AI / Elementos de IA, creados por Reaktor Innovations y la Universidad de Helsinki.
* Los mensajes de la demo son ejemplos inventados. No he copiado código, imágenes ni datos de terceros sin permiso.
