## Info
```Typescript
//  1) Per rendere tutte le proprietà delle interfacce opzionali
export interface Customer {
    id: number;
    name: string;
    age: number;
}

// using the interface but make all fields optional
import { Customer } from './api.model';

export class MyComponent {
    cust: Partial<Customer>;  /

    ngOninit() {
        this.cust = { name: 'jane' }; // no error throw because all fields are optional
    }
}


// 2) Eliminare l'errore
if (false) {
    // @ts-ignore
    console.log('x');
}
```