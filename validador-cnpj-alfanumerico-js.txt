class Cnpj {
    static multiplicador1 = [5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2];
    static multiplicador2 = [6, 5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2];

    static valido(value) {
        if (!value) {
            return false;
        }
        
        value = value.replace(/[.\/-]/g, '');

        let digitosIdenticos = true;
        let ultimoDigito = 0;
        let posicao = 0;
        let totalDigito1 = 0;
        let totalDigito2 = 0;

        const isValidInput = (c) => /[0-9A-Z]/.test(c);

        for (let c of value) {
            if (isValidInput(c)) {
                let digito = c.charCodeAt(0) - 48;
                if (posicao !== 0 && ultimoDigito !== digito) {
                    digitosIdenticos = false;
                }

                ultimoDigito = digito;
                if (posicao < 12) {
                    totalDigito1 += digito * this.multiplicador1[posicao];
                    totalDigito2 += digito * this.multiplicador2[posicao];
                } else if (posicao === 12) {
                    let dv1 = totalDigito1 % 11;
                    dv1 = dv1 < 2 ? 0 : 11 - dv1;

                    if (digito !== dv1) {
                        return false;
                    }

                    totalDigito2 += dv1 * this.multiplicador2[12];
                } else if (posicao === 13) {
                    let dv2 = totalDigito2 % 11;
                    dv2 = dv2 < 2 ? 0 : 11 - dv2;

                    if (digito !== dv2) {
                        return false;
                    }
                }
                posicao++;
            }
        }
        return posicao === 14 && !digitosIdenticos;
    }
}

class Program {
    static main() {
        const validos = [
            "12ABC34501DE35", "C1RY36DY000120", "98.335.506/0001-56", "RO074JUC000188", 
            "EEXVC28G000120", "Q5INVTLF000172", "A6GG1NJV000162", "35.891.619/0001-18"
        ];
        
        const invalidos = [
            "AAAAAAA11111111", "123456789ABCDE", "X1Y2Z3W4V5U6X9", "00000000000000", "ABCDEFGHIJKLMN"
        ];
        
        console.log("Devem ser válidos:");
        validos.forEach(cnpj => {
            console.log(`${cnpj}: ${Cnpj.valido(cnpj) ? "VALIDO - CORRETO" : "INVALIDO - ERRADO"}`);
        });
        
        console.log("\nDevem ser inválidos:");
        invalidos.forEach(cnpj => {
            console.log(`${cnpj}: ${Cnpj.valido(cnpj) ? "VALIDO - ERRADO" : "INVALIDO - CORRETO"}`);
        });
    }
}

Program.main();
