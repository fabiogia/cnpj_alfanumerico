using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Text.RegularExpressions;
					
public class Program
{
	public static void Main()
	{	 
		string[] validos   = { "12ABC34501DE35", "C1RY36DY000120", "98.335.506/0001-56", "RO074JUC000188", "EEXVC28G000120", "Q5INVTLF000172", "A6GG1NJV000162", "35.891.619/0001-18" };
		string[] invalidos = { "AAAAAAA11111111", "123456789ABCDE", "X1Y2Z3W4V5U6X9", "00000000000000", "ABCDEFGHIJKLMN" };
	
		Console.WriteLine("Devem ser validos:");
		foreach (string cnpj in validos)
			Console.WriteLine(cnpj + ": " + (Cnpj.Valido(cnpj) ? "VALIDO - CORRETO" : "INVALIDO - ERRADO"));
		
		Console.WriteLine("");
		Console.WriteLine("Devem ser invalidos:");
		foreach (string cnpj in invalidos)
			Console.WriteLine(cnpj + ": " + (Cnpj.Valido(cnpj) ? "VALIDO - ERRADO" : "INVALIDO - CORRETO"));
	}
}	

public static class Cnpj
{
	static readonly int[] Multiplicador1 = [5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2];
	static readonly int[] Multiplicador2 = [6, 5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2];

	public static bool Valido(string? value)
	{
		if (value == null)
		{
			return false;
		}     
      string pattern = @"[\/\-.]"; // Regex para remover /, - e .
      value = Regex.Replace(value, pattern, "");

		var digitosIdenticos = true;
		var ultimoDigito = 0;
		var posicao = 0;
		var totalDigito1 = 0;
		var totalDigito2 = 0;

		static bool IsValidInput(char c) =>
			char.IsAsciiDigit(c) || char.IsAsciiLetterUpper(c);

		foreach (var c in value!)
		{
			if (IsValidInput(c))
			{
				var digito = c - '0';
				if (posicao != 0 && ultimoDigito != digito)
				{
					digitosIdenticos = false;
				}

				ultimoDigito = digito;
				if (posicao < 12)
				{
					totalDigito1 += digito * Multiplicador1[posicao];
					totalDigito2 += digito * Multiplicador2[posicao];
				}
				else if (posicao == 12)
				{
					var dv1 = (totalDigito1 % 11);
					dv1 = dv1 < 2
						? 0
						: 11 - dv1;

					if (digito != dv1)
					{
						return false;
					}

					totalDigito2 += dv1 * Multiplicador2[12];
				}
				else if (posicao == 13)
				{
					var dv2 = (totalDigito2 % 11);

					dv2 = dv2 < 2
						? 0
						: 11 - dv2;

					if (digito != dv2)
					{
						return false;
					}
				}
				posicao++;
			}
		}
		return (posicao == 14) && !digitosIdenticos;
	}
}
