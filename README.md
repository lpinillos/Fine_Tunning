# Fine-Tuning con LoRA, PEFT y RLHF

## Resumen

Este documento presenta, de forma clara y práctica, tres enfoques para adaptar modelos de lenguaje grandes (LLMs) a tareas específicas: **LoRA**, **PEFT** y **RLHF**. La idea es entender **qué son**, **para qué sirven** y **cuándo conviene usarlos**, sin entrar en excesivos detalles técnicos.

---

## ¿Por qué afinar un LLM?

* Cuando el *prompting* y el *retrieval* (RAG) no bastan para lograr el **estilo**, **formato** o **comportamiento** deseado.
* Para especializar un modelo base en un **dominio concreto** (legal, médico, servicio al cliente, etc.).
* Para **alinear** el comportamiento del modelo con **preferencias humanas** (seguridad, tono, brevedad).

---

## LoRA (Low-Rank Adaptation)

**Qué es:** Una técnica de *fine-tuning eficiente* que **congela** los pesos del modelo base y añade **adaptadores** pequeños (de “bajo rango”) en capas específicas, entrenando solo esos parámetros adicionales.

**Cómo funciona (idea general):**

* El modelo base no se modifica (se “congela”).
* Se insertan módulos ligeros que aprenden una **corrección** a las proyecciones más importantes (p. ej., en la atención).
* Al final, puedes **fusionar** esos adaptadores y servir el modelo sin penalización de latencia.

**Cuándo usarlo:**

* Cuando hay **recursos limitados** (GPU/VRAM) y se necesita una adaptación rápida y económica.
* Cuando se requieren **múltiples variantes** del mismo modelo para distintas tareas/clientes (cada una con su adapter).

**Ventajas clave:**

* Muchísimos menos parámetros entrenables → **menos VRAM** y **menor costo**.
* Posibilidad de **mantener y versionar adapters** por tarea, sin duplicar el modelo base.

---

## PEFT (Parameter-Efficient Fine-Tuning)

**Qué es:** Un **marco** (umbrella) de técnicas de afinado eficiente (incluye LoRA, Prefix/Prompt Tuning, IA³, etc.) y utilidades para **inyectar**, **gestionar** y **activar/desactivar** adaptadores de forma sencilla (p. ej., la librería PEFT de Hugging Face).

**Para qué sirve:**

* Facilita la **gestión práctica** de adapters: guardar, cargar, intercambiar y combinar según la tarea.
* Permite **compartir** adapters sin redistribuir el modelo completo.
* Encaja muy bien en ciclos de producto con múltiples clientes/verticales.

**Cuándo usarlo:**

* Cuando quieres adoptar **buenas prácticas operativas**: un **modelo base único** + **varios adapters**.
* Cuando buscas **flexibilidad** y **mantenimiento simple** en producción.

---

## RLHF (Reinforcement Learning from Human Feedback)

**Qué es:** Un proceso para **alinear** el comportamiento del modelo con **preferencias humanas** mediante retroalimentación. Suele incluir:

1. **SFT** (entrenamiento supervisado inicial con demostraciones humanas).
2. Entrenamiento de un **modelo de recompensa** con comparaciones A/B (qué respuesta prefieren los humanos).
3. **Optimización** del modelo con RL (p. ej., PPO) o variantes más simples como **DPO** (que evita el modelo de recompensa explícito).

**Cuándo usarlo:**

* Cuando el *qué responde* ya es razonable, pero el *cómo responde* (tono, cortesía, seguridad, brevedad, rechazos adecuados) es crítico.
* Cuando puedes costear **etiquetado humano** y validación más exigente.

**Ventajas clave:**

* Mejora la **utilidad real** y la **seguridad** del modelo según criterios humanos.
* Permite ajustar **preferencias finas** que el SFT por sí solo no logra.

---

## ¿Cuál elijo?

* **LoRA** → afinado **rápido y barato** para **especializar** un modelo; excelente primera opción.
* **PEFT** → **operar** LoRA (y otras técnicas) a escala: **gestión de adapters**, versionado, A/B, canary.
* **RLHF** → cuando necesitas **alineación** con preferencias humanas y **puedes etiquetar** comparaciones.

---

## Flujo sugerido (alto nivel)

1. **Definir objetivo** y métricas (qué significa “bueno”).
2. Probar primero **prompting/RAG**; si no alcanza, seguir.
3. **SFT + LoRA (vía PEFT)** con datos curados y validación separada.
4. Medir con **conjuntos de prueba** y *golden prompts* del negocio.
5. Si el comportamiento requiere matices humanos → **SFT + RLHF/DPO**.
6. Desplegar **base + adapter** (o fusionar) con monitoreo de calidad.

---

## Buenas prácticas mínimas

* **Datos:** limpios, balanceados, sin *leakage*; separar *train/val/test*.
* **Entrenamiento:** congelar base, *early stopping*, *learning rate* moderado.
* **Evaluación:** pruebas automáticas + revisión humana enfocada al caso de uso.
* **Producción:** versionar adapters, habilitar *rollbacks* y monitoreo de drift.

---

## Referencias (para mayor profundidad)

* LoRA práctico:
  [https://medium.com/@manindersingh120996/practical-guide-to-fine-tune-llms-with-lora-c835a99d7593](https://medium.com/@manindersingh120996/practical-guide-to-fine-tune-llms-with-lora-c835a99d7593)
* Panorama PEFT (adapters, configuración):
  [https://medium.com/@sulbha.jindal/llm-fine-tuning-with-peft-a6bdfe789323](https://medium.com/@sulbha.jindal/llm-fine-tuning-with-peft-a6bdfe789323)
* RLHF (técnicas y mejores prácticas):
  [https://medium.com/@meeran03/fine-tuning-llms-with-human-feedback-rlhf-latest-techniques-and-best-practices-3ed534cf9828](https://medium.com/@meeran03/fine-tuning-llms-with-human-feedback-rlhf-latest-techniques-and-best-practices-3ed534cf9828)
* Intuición de LoRA (low-rank):
  [https://ai.plainenglish.io/understanding-low-rank-adaptation-lora-for-efficient-fine-tuning-of-large-language-models-082d223bb6db](https://ai.plainenglish.io/understanding-low-rank-adaptation-lora-for-efficient-fine-tuning-of-large-language-models-082d223bb6db)
* Video introductorio (LoRA/PEFT/RLHF):
  [https://www.youtube.com/watch?v=Us5ZFp16PaU](https://www.youtube.com/watch?v=Us5ZFp16PaU)

---
