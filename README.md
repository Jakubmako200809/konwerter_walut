using System;
using System.Collections.Generic;

public interface ICurrencyConverter
{
    decimal ConvertCurrency(
        decimal amount,
        string fromCurrency,
        string toCurrency);
}


public class FixedRateCurrencyConverter : ICurrencyConverter
{
    private readonly Dictionary<string, decimal> _rates;

    public FixedRateCurrencyConverter()
    {
        _rates = new Dictionary<string, decimal>
        {
            { "PLN", 1.00m },
            { "EUR", 4.30m },
            { "USD", 3.95m },
            { "GBP", 5.10m }
        };
    }

    public decimal ConvertCurrency(
        decimal amount,
        string fromCurrency,
        string toCurrency)
    {
        fromCurrency = fromCurrency.ToUpper();
        toCurrency = toCurrency.ToUpper();

        if (!_rates.ContainsKey(fromCurrency))
            throw new ArgumentException(
                $"Nieznana waluta: {fromCurrency}");

        if (!_rates.ContainsKey(toCurrency))
            throw new ArgumentException(
                $"Nieznana waluta: {toCurrency}");
        
        decimal amountInPLN = amount * _rates[fromCurrency];
        decimal result = amountInPLN / _rates[toCurrency];

        return result;
    }
}


public class LiveCurrencyConverter : ICurrencyConverter
{
    private readonly Dictionary<string, decimal> _rates;

    public LiveCurrencyConverter()
    {
        _rates = new Dictionary<string, decimal>
        {
            { "PLN", 1.00m },
            { "EUR", 4.28m },
            { "USD", 3.92m },
            { "GBP", 5.05m }
        };
    }

    public decimal ConvertCurrency(
        decimal amount,
        string fromCurrency,
        string toCurrency)
    {
        fromCurrency = fromCurrency.ToUpper();
        toCurrency = toCurrency.ToUpper();

        if (!_rates.ContainsKey(fromCurrency))
            throw new ArgumentException(
                $"Nieznana waluta: {fromCurrency}");

        if (!_rates.ContainsKey(toCurrency))
            throw new ArgumentException(
                $"Nieznana waluta: {toCurrency}");

        decimal amountInPLN = amount * _rates[fromCurrency];
        decimal result = amountInPLN / _rates[toCurrency];

        return result;
    }
}

public class CurrencyConversionApp
{
    private readonly ICurrencyConverter _currencyConverter;

    public CurrencyConversionApp(ICurrencyConverter currencyConverter)
    {
        _currencyConverter = currencyConverter;
    }

    public void Convert(
        decimal amount,
        string fromCurrency,
        string toCurrency)
    {
        decimal result = _currencyConverter.ConvertCurrency(
            amount,
            fromCurrency,
            toCurrency);

        Console.WriteLine(
            $"{amount:F2} {fromCurrency.ToUpper()} = " +
            $"{result:F2} {toCurrency.ToUpper()}");
    }
}


public class Program
{
    public static void Main()
    {
        ICurrencyConverter fixedConverter =
            new FixedRateCurrencyConverter();

        CurrencyConversionApp fixedApp =
            new CurrencyConversionApp(fixedConverter);

        Console.WriteLine("=== Stałe kursy ===");

        fixedApp.Convert(100, "PLN", "EUR");
        fixedApp.Convert(100, "EUR", "USD");
        fixedApp.Convert(50, "GBP", "PLN");

        
        ICurrencyConverter liveConverter =
            new LiveCurrencyConverter();

        CurrencyConversionApp liveApp =
            new CurrencyConversionApp(liveConverter);

        Console.WriteLine();
        Console.WriteLine("=== Kursy Live ===");

        liveApp.Convert(100, "PLN", "EUR");
        liveApp.Convert(100, "EUR", "USD");
        liveApp.Convert(50, "GBP", "PLN");
    }
}
