# Sugestões de padrões e boas práticas para testes em Frontends

## Utilizar mutation testing
Verificar a qualidade dos testes unitários utilizando algum teste de mutação. Esses testes fazem alterações no código fonte da aplicação e verificam se os testes são capazes de capturar erros.

Utilizamos o [Stryker Mutator](https://stryker-mutator.io) para fazer os testes de mutação:

Instalando e executando o Stryker:
https://stryker-mutator.io/stryker/quickstart

## Omitir informações que não fazem parte do teste
**Ruim:** a construção da variável 'obj' dentro do teste apenas dificulta a leitura e a futura manutenção do mesmo.
```javascript
describe('HelloWorldComponent', () => {
  it('deve testar foo', () => {
      const obj = {
        prop1: 'hello',
        prop2: 'world',
      };
      
      const result = myClass.foo(obj);
      expect(result).toBe(false);
  });
}
```

**Bom:** Omitir as informações que não fazem sentido para o teste, como inicialização de variáveis, configurações de componentes etc. Tomar cuidado para sempre criar novos objetos para cada teste.
```javascript
describe('HelloWorldComponent', () => {
  function criarObj() {
    return {
        prop1: 'hello',
        prop2: 'world',
      };
  }

  it('deve testar foo', () => {
      let obj = criarObj();
      const result = myClass.foo(obj);
      expect(result).toBe(false);
  });
}
```

## Utilização de variáveis globais
Declarar variáveis, classes, funções, etc fora do escopo do teste.

## Testes unitários não unitários
Evitar que os testes unitários executem regras que não fazem parte do escopo do teste.

**Ruim** O método 'foo' precisa de um parâmetro que é calculado pela própria classe. Ao utilizar a classe para gerar o valor de 'dado1', deixamos de testar apenas o comportamento de 'foo'. Além disso, irá gerar uma falsa cobertura de teste, pois o método hello() terá cobertura, mas não necessariamente existe um teste para eles.
```javascript
describe('HelloWorldComponent', () => {
  it('deve testar foo', () => {
      const dado1 = myClass.hello();
      const result = myClass.foo(dado1);
      expect(result).toBe(false);
  });
}
```

**Bom** Ao criar um mock de 'dado1', iremos testar apenas o comportamento de 'foo'.
```javascript
describe('HelloWorldComponent', () => {
  function mockObj() {
    return {
        prop1: 'hello',
        prop2: 'world',
      };
  }
  it('deve testar foo', () => {
      const dado1 = mockObj();
      const result = myClass.foo(dado1);
      expect(result).toBe(false);
  });
}
```
