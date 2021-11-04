# Estudos de angular e docker
## Introdução

Neste projeto, para subir o projeto angular em um container do docker, é utilizada a composição de containers por meio do docker-compose.

Nota-se a presença de um arquivo docker-compose.yml para a composição do nosso container, e o arquivo Dockerfile, necessário para instruir a criação das imagens a serem criadas a partir do projeto angular utilizando uma imagem do nodejs e do iginx.

# Execução do projeto

1 - Abrir o terminal na pasta estudo-angular-a;

2 - Executar o compando __docker-compose up -d__;

O comando irá:
- Criar um container para execução;
- Instalar as dependências;
- Realizar o build da aplicação;
- Criar um projeto nginx com o build do passo anterior;
- Expor a porta 80 para acessar a aplicação via navegador.

# Angular

## Criações
A criação de elementos dentro do angular acontece por comandos no terminal.

__ng generate elemento__

Podendo ainda utilizar o comando resumido.

__ng g elemento__

### Componentes
__ng generate component diretorio/do/componente__

__ng g c diretorio/do/componente__

### Services
__ng g services diretorio/do/componente__

__ng g s__

## Exportação de módulos

É possível exportar um módulo inteiro através de um arquivo typescript com a seguinte linha de código:
__export * from './diretório/do/módulo';__

Dentro do arquivo **app.modulo.ts** basta importar o módulo pelo caminho do arquivo criado no passo anterior.

## Ciclo de vida das instancias dos componentes

Os componentes do angular são instanciados e possuem um ciclo de vida, desde o seu inicio quando ocorre o evento ngOnInit até o ngOnDestroy.

[Documentação Angular - Ciclo de Vida](https://angular.io/guide/lifecycle-hooks)

### ngOnInit()

implementação e método obrigatório:

__implements OnInit__
__ngOnInit()__

A classe possui o método construtor para a criação da instancia.
O método ngOnInit é chamado assim que a instancia do componente é criada, a utilização deste método contra o método construtor é recomendada em casos onde a inicialização do componente necessita de alguma operação de maior custo, possibilitando assim, que o componente seja instanciado sem problemas e posteriormente execute a ação contida em ngOnInit().

### ngOnChanges()

### ngDoCheck()

# Comunicação de Componentes
## Vínculos Dentro do Componente
A comunicação dentro do angular acontece baseada no estado da aplicação, de modo que os vinculos estarão sendo atualizados a cada nova interação realizada.

### One Way Data Binding
É possível utilizar uma expressão proveniente do typescript do componente dentro do html. Para tal, utilizamos uma das seguintes sintaxes:
```typescript
[valor]="expressão"

{{expressão}}

vinculo-alvo="expressão"
```
[Documentação Angular - Sintaxe de Vinculos](https://angular.io/guide/binding-syntax)

### Event Biding
Chamadas a eventos podem ser realizadas diretamente dentro do html do componente.
```
(click)="event"
```
[Documentação Angular - Vínculo de Evento](https://angular.io/guide/event-binding)

## Vinculação Entre Componentes
É possível configurar propiedades de entrada e saída de um componenete com os decoradores input e output.

### Comunicação do Componente Pai para o Componente Filho
[Documentação Angular - Comunicação do Componente Pai para Filho](https://angular.io/guide/component-interaction#pass-data-from-parent-to-child-with-input-binding)
Para a configuração de uma entrada de dados no componenter filho, é necessário a utilização do decorador **@Input()**. 

#### Configurando o Componente Filho
- Importar o Input com a seguinte linha de código:
```typescript
import { Component, Input } from '@angular/core';
//A importação do Input é feita pela mesma pasta do Component.

```
- Declarar a propriedade a ser recebida externamente pelo componente com o decorador @Input():
```typescript
export class FilhoComponent {
    @Input() hero = '';
}
```
O decorador **@Input()** é capaz de ser utilizado com qualquer tipo de dados.

O código a seguir é referente a um componente filho genérico.
```typescript
import { Component, Input } from '@angular/core';
import { Hero } from './hero';

@Component({
  selector: 'app-hero-child',
  templateUrl: `
    <h3>{{hero.name}} says:</h3>
    <p>I, {{hero.name}}, am at your service, {{masterName}}.</p>
  `
})
export class HeroChildComponent {
  @Input() hero!: Hero;
  @Input('master') masterName = ''; // tslint:disable-line: no-input-rename
}
```
No código é utilizado o decorador @Input para declarar uma entrada de dados, possibilitando que o componente pai possa realizar a comunicação através das propriedades **hero** e **master**.
A utilização do decorador com parâmetro **@Input('master')** renomeia a entrada de dados para ser utilizada pelo componente pai.

#### Passando Parâmetros do Componente Pai
Para passar os parâmetros definidos anteriormente no componente filho, faz-se como em uma [vinculação de sentido único](#one-way-data-binding). Não é necessário nenhuma importação adicional por parte do componente pai.
```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-hero-parent',
  template: `
    <h2>{{master}} controls {{heroes.length}} heroes</h2>

    <app-hero-child
      *ngFor="let hero of heroes"
      [hero]="hero"
      [master]="master">
    </app-hero-child>
  `
})
export class HeroParentComponent {
  heroes = ['Dr IQ', 'Magneta', 'Bombasto'];
  master = 'Master';
}
```
Desse modo serão geradas 3 tags **app-hero-child**. Onde são passados os parâmetros do componente atual (Pai) heroes e master para as propriedades decoradas com o **@Input()** do componente filho. Os parâmetros do componente filho configurados como entradas são acessado pelo componente pai por meio das chaves **[propriedade]** e configuradas com a utilização do operador de atribuição **=**.

[Documentação Angular - Entradas e Saídas](https://angular.io/guide/inputs-outputs#input)

#### Configurações Adicionais do Componente Filho
É possível fazer tratamentos nas propriedades recebidas no componente filho. No exemplo a seguir, o componente filho configura a entrada pela função set que, no caso, remove os espaços excedentes.
```typescript
export class NameChildComponent {
  @Input()
  get name(): string { return this._name; }
  set name(name: string) {
    this._name = (name && name.trim()) || '<no name set>';
  }
  private _name = '';
}
```
[Documentação Angular - Interceptando a entrada com Setter](https://angular.io/guide/component-interaction#intercept-input-property-changes-with-a-setter)

## Recebendo Evento do Componente Filho no Componente Pai
[Documentação Angular - Componente Pai Ouvindo um Filho](https://angular.io/guide/component-interaction#parent-listens-for-child-event)
O componente filho deve exportar uma propriedade [EventEmitter](https://angular.io/api/core/EventEmitter) que será responsável por emitir(*emits*) um evento quando algo acontecer. Onde o componente Pai irá configurar um vínculo com tal propriedade, para reagir de acordo com os eventos ocorridos.

### Configuração do Componente Filho
Dentro da classe do componente filho a propriedade do tipo EventEmitter deve ser decorada com **@Output()**, e não precisa ser declarada dentro do template(HTML) do componente. Sua chamada ocorre ainda dentro do componente filho e será comunicada para o componente pai.
```typescript
import { Component, EventEmitter, Input, Output } from '@angular/core';

@Component({
  selector: 'app-voter',
  template: `
    <h4>{{name}}</h4>
    <button (click)="vote(true)"  [disabled]="didVote">Agree</button>
    <button (click)="vote(false)" [disabled]="didVote">Disagree</button>
  `
})
export class VoterComponent {
  @Input()  name = '';
  @Output() voted = new EventEmitter<boolean>();
  didVote = false;

  vote(agreed: boolean) {
    this.voted.emit(agreed);
    this.didVote = true;
  }
}
```

### Configurando o Componente Pai
O componente pai define a propriedade **name** do componente filho utilizando *[name]=voter*, e na linha seguinte (assim como em [Vínculo de Eventos](#event-biding)), o evento *(voted)* do componente filho realizará a chamada do evento *onVoted()* do componente pai, passando os argumentos do evento com *$event*.

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-vote-taker',
  template: `
    <h2>Should mankind colonize the Universe?</h2>
    <h3>Agree: {{agreed}}, Disagree: {{disagreed}}</h3>

    <app-voter
      *ngFor="let voter of voters"
      [name]="voter"
      (voted)="onVoted($event)">
    </app-voter>
  `
})
export class VoteTakerComponent {
  agreed = 0;
  disagreed = 0;
  voters = ['Narco', 'Celeritas', 'Bombasto'];

  onVoted(agreed: boolean) {
    agreed ? this.agreed++ : this.disagreed++;
  }
}
```

## Testes

Os arquivos .spec.ts possuem o objetivo de testar os códigos.
Todos os testes são declarados dentro do arquivo mencionado, e são feitos pela função __it__ proveniente do [Jasmine](https://jasmine.github.io/index.html), uma biblioteca javascript direcionada a realização de testes com suporte a BDD (Behaviour Driven Development) e utilizado com TDD (Test Driven Development).

### JASMINE

Como exemplo testar uma função de soma.
```typescript
describe('Calculadora',() ={
    it('deve garantir que 1+4=5',
        inject([Calculadora], (service: Calculadora) => {
            let soma = service.somar(1,4);
            expect(soma).toEqual(5);
        }
        ))
)}
```

describe(string, function): escopo do teste

it(string, function): nome do teste

expect(actual): chamada do evento/function a ser testado

### Expect
| Comandos |
| ------- |
|expect(array).toContain(member);|
|expect(fn).toThrow(string);|
|expect(fn).toThrowError(string);|
|expect(instance).toBe(instance);|
|expect(mixed).toBeDefined();|
|expect(mixed).toBeFalsy();|
|expect(mixed).toBeNull();|
|expect(mixed).toBeTruthy();|
|expect(mixed).toBeUndefined();|
|expect(mixed).toEqual(mixed);|
|expect(mixed).toMatch(pattern);|
|expect(number).toBeCloseTo(number, decimalPlaces);|
|expect(number).toBeGreaterThan(number);|
|expect(number).toBeLessThan(number);|
|expect(number).toBeNaN();|
|expect(spy).toHaveBeenCalled();|
|expect(spy).toHaveBeenCalledTimes(number);|
|expect(spy).toHaveBeenCalledWith(...arguments);|