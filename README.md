Para criar um manipulador de gatilho, você simplesmente precisa criar uma classe que herda do TriggerHandler.cls. Aqui está um exemplo para criar um manipulador de gatilhos opportunity.

public class OpportunityTriggerHandler extends TriggerHandler {
No seu manipulador de gatilho, para adicionar lógica a qualquer um dos contextos do gatilho, você só precisa substituí-los no seu manipulador de gatilho. Aqui está como adicionaríamos lógica a um gatilho.beforeUpdate

public class OpportunityTriggerHandler extends TriggerHandler {
  
  public override void beforeUpdate() {
    for(Opportunity o : (List<Opportunity>) Trigger.new) {
      // do something
    }
  }

  // add overrides for other contexts

}
Nota: Ao fazer referência às estáticas do Gatilho dentro de uma classe, os SObjects são devolvidos contra subclasses SObject como Opportunity, Account, etc. Isso significa que você deve lançar quando você referencia-los em seu manipulador de gatilho. Você poderia fazer isso em seu construtor se quisesse.

public class OpportunityTriggerHandler extends TriggerHandler {

  private Map<Id, Opportunity> newOppMap;

  public OpportunityTriggerHandler() {
    this.newOppMap = (Map<Id, Opportunity>) Trigger.newMap;
  }
  
  public override void afterUpdate() {
    //
  }

}
Para usar o manipulador de gatilho, você só precisa construir uma instância do seu manipulador de gatilho dentro do próprio manipulador de gatilho e chamar o método. Aqui está um exemplo do gatilho opportunity.run()

trigger OpportunityTrigger on Opportunity (before insert, before update) {
  new OpportunityTriggerHandler().run();
}
Coisas legais
Contagem de loops máximos
Para evitar a recursão, você pode definir uma contagem máxima de loop para o Manipulador de Gatilho. Se este máximo for excedido, e exceção será lançada. Um ótimo caso de uso é quando você deseja garantir que o seu gatilho funcione uma e única vez dentro de uma única execução. Exemplo:

public class OpportunityTriggerHandler extends TriggerHandler {

  public OpportunityTriggerHandler() {
    this.setMaxLoopCount(1);
  }
  
  public override void afterUpdate() {
    List<Opportunity> opps = [SELECT Id FROM Opportunity WHERE Id IN :Trigger.newMap.keySet()];
    update opps; // this will throw after this update
  }

}
Bypass API
E se você quiser dizer a outros manipuladores de gatilho para parar a execução? Isso é fácil com o bypass api:

public class OpportunityTriggerHandler extends TriggerHandler {
  
  public override void afterUpdate() {
    List<Opportunity> opps = [SELECT Id, AccountId FROM Opportunity WHERE Id IN :Trigger.newMap.keySet()];
    
    Account acc = [SELECT Id, Name FROM Account WHERE Id = :opps.get(0).AccountId];

    TriggerHandler.bypass('AccountTriggerHandler');

    acc.Name = 'No Trigger';
    update acc; // won't invoke the AccountTriggerHandler

    TriggerHandler.clearBypass('AccountTriggerHandler');

    acc.Name = 'With Trigger';
    update acc; // will invoke the AccountTriggerHandler

  }

}
Se você precisar verificar se um manipulador está ignorado, use o método:isBypassed

if (TriggerHandler.isBypassed('AccountTriggerHandler')) {
  // ... do something if the Account trigger handler is bypassed!
}
Se você quiser limpar todos os desvios para a transação, use o método simples, como em:clearAllBypasses

// ... done with bypasses!

TriggerHandler.clearAllBypasses();

// ... now handlers won't be ignored!
Métodos superáveis
Aqui estão todos os métodos que você pode substituir. Todas as possibilidades de contexto são suportadas.

beforeInsert()
beforeUpdate()
beforeDelete()
afterInsert()
afterUpdate()
afterDelete()
afterUndelete()
