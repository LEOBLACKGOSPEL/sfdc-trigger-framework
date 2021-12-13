Os gatilhos devem (IMO) sem lógica. Colocar lógica em seus gatilhos cria códigos intestáveis e difíceis de manter. É amplamente aceito que uma prática recomendada é mover a lógica do gatilho para uma classe de manipulador.

Esta estrutura de gatilho agrupa uma única classe base TriggerHandler da a sua vez em todos os seus manipuladores de gatilho. A classe base inclui métodos específicos de contexto que são automaticamente chamados quando um gatilho é executado.

A classe base também fornece um papel secundário como supervisor para execução do Gatilho. Atua como um cão de guarda, monitorando a atividade do gatilho e fornecendo uma api para controlar certos aspectos da execução e fluxo de controle.

Mas a parte mais importante deste quadro é que é mínimo e simples de usar.
