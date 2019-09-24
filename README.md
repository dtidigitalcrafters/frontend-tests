# Sugestões de padrões e boas práticas para testes em Frontends

## Utilização de variáveis globais
Declarar variáveis, classes, funções, etc fora do escopo do teste.

## Omitir informações que não fazem parte do teste
**Ruim:** a construção da variável 'obj' dentro do teste apenas dificulta a leitura e a futura manutenção do mesmo.
```javascript
describe('PadronizacaoViComponent', () => {
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
describe('PadronizacaoViComponent', () => {
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

## Utilizar mutation testing
Verificar a qualidade dos testes unitários utilizando algum teste de mutação. Esses testes fazem alterações no código fonte da aplicação e verificam se os testes são capazes de capturar erros.
