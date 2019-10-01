# Padrões e boas práticas para testes em Frontends

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
  it('deve testar foo', () => {
      let obj = criarObj();
      const result = myClass.foo(obj);
      expect(result).toBe(false);
  });  
  function criarObj() {
    return {
        prop1: 'hello',
        prop2: 'world',
      };
  }
}
```

## Evitar utilizar variáveis globais
Evite declarar variáveis, classes, funções, etc fora do escopo do teste.

## Evitar fazer testes unitários não unitários
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
  it('deve testar foo', () => {
      const dado1 = mockObj();
      const result = myClass.foo(dado1);
      expect(result).toBe(false);
  });
  function mockObj() {
    return {
        prop1: 'hello',
        prop2: 'world',
      };
  }
}
```

## Declarar as configurações gerais, os testes e depois as funções de suporte
Se estamos trabalhando com um arquivo de testes, a parte mais importante são os testes em si. Declarar as variáveis e funções de suporte antes dos testes pode dificultar a leitura dos mesmos.

**Ruim**
```javascript
describe('HelloWorldComponent', () => {  
  function mockObj() {
    return {
        prop1: 'hello',
      };
  }
  
  const serviceMock = {
    method1 () => {};
    method2 () => {};
  };
  
  beforeEach(() => {
    ...
  });
  
  it('deve testar foo', () => {
      const dado1 = mockObj();
      const result = myClass.foo(dado1);
      expect(result).toBe(false);
  });
}
```

**Bom**
```javascript
describe('HelloWorldComponent', () => {  
  beforeEach(() => {
    ...
  });
  
  it('deve testar foo', () => {
      const dado1 = mockObj();
      const result = myClass.foo(dado1);
      expect(result).toBe(false);
  });
  
  function mockObj() {
    return {
        prop1: 'hello',
      };
  }
  
  const serviceMock = {
    method1 () => {};
    method2 () => {};
  };
}
```

## compileComponents() é assíncrono
O método compileComponents() é assíncrono e deve ser utilizado em uma chamada beforeEach separada, dentro de um método async(), para garantir que os componentes foram compilados, antes de criarmos uma fixture.

```javascript
beforeEach(async(() => {
    TestBed.configureTestingModule({
      imports: [
      ],
      declarations: [
        AppComponent
      ],
    }).compileComponents();
  }));
  
beforeEach(() => {
  fixture = TestBed.createComponent(AppComponent);
  component = fixture.componentInstance;
  fixture.detectChanges();
}
```

## NO_ERRORS_SCHEMA
Componentes geralmente possuem outros componentes aninhados dentro, que precisam ser declarados para o correto funcionamento do mesmo.
Ao utilizar NO_ERRORS_SCHEMA na configuração de nosso TestBed, informamos ao angular para ignorar quaisquer tags inválidas que forem encontradas durante os testes.

**Ruim**
```javascript
beforeEach(async(() => {
    TestBed.configureTestingModule({
      imports: [
      ],
      declarations: [
        AppComponent,
        HeaderComponent,
        FooterComponent
      ],
      schemas: [ NO_ERRORS_SCHEMA ],
    }).compileComponents();
  }));
```

**Bom**
```javascript
beforeEach(async(() => {
    TestBed.configureTestingModule({
      imports: [
      ],
      declarations: [
        AppComponent
      ],
      schemas: [ NO_ERRORS_SCHEMA ],
    }).compileComponents();
  }));
```
