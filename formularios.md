# Formulários
## Estratégias de Formulários
[Documentação Oficial Angular](https://angular.io/guide/forms-overview)
### Reactive
#### FormGroup
[Documentação Oficial Angular](https://angular.io/api/forms/FormGroup)
Utilizado para agrupar um conjunto de FormControls.
O FormsGroup capacita o **acompanhamento dos valores e do estado** de um formulário.
Recomenda-se a inicialização de um FormGroup dentro do método ngOnInit().
```typescript
import { FormGroup, FormBuilder } from '@angular/forms'

@Component({})
export class AppComponent implements OnInit {

    fg: FormGroup;
    
    constructor(private fb: FormBuilder){}
    
    ngOnInit(){
        this.initializeForm();
    }
    
    initializeForm(): void{
        this.fg = this.fb.group({
            name: 'Your Name Here',
            age: 'Your Age Here'
        });
    }
}
```
Na inicialização do FormGroup pelo FormBuilder, espera-se como parametro um objeto chave-valor, onde cada objeto representa um [**FormControl**](#formcontrol), onde o objeto é representado como:
| Chave | Valor |
| ----- | ----- |
| Name | Control |
No exemplo acima a chave será relacionada coma a propriedade **controlFormName** de cada elemento do *HTML* e o valor de cada chave representa o valor inicial do campo no formulário.
```typescript
    initializeForm(): void{
        this.fg = this.fb.group({
            name: 'Your Name Here',
            age: 'Your Age Here',
            hobbys: this.fb.group({
                video-game: false,
                music: false,
                read: false
            })
        });
    }
}
```
Ainda se torna possível a construção de [FormGroups](#formarray) aninhados.

#### FormBuilder
[Documentação Oficial Angular](https://angular.io/api/forms/FormBuilder)
Reduz significativamente a quantidade de código necessária para a geração de [FormControls](#formcontrol)

#### FormControl
[Documentação Oficial Angular](https://angular.io/api/forms/FormControl)
Bloco de maior atomicidade na construção de formulários.

#### FormArray
[Documentação Oficial Angular](https://angular.io/api/forms/FormArray)
Um FormArray agrupa de maneira dinâmica elementos FormControls para a construção de formulários.

### Template-Driven









