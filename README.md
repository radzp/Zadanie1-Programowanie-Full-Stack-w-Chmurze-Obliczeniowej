# Zadanie1-Programowanie-Full-Stack-w-Chmurze-Obliczeniowej

# ----- Część obowiązkowa -----
## Tworzenie klastra:

<img width="741" height="24" alt="Zrzut ekranu 2025-12-16 o 1 51 01 AM" src="https://github.com/user-attachments/assets/2c4ed1f9-efdb-4026-b955-7308cf15fd6f" />

<img width="518" height="132" alt="Zrzut ekranu 2025-12-16 o 1 51 13 AM" src="https://github.com/user-attachments/assets/40266aaa-1b4b-4ee4-b572-ac0a7b2e6174" />

## Labeling: 
<img width="596" height="214" alt="Zrzut ekranu 2025-12-16 o 1 51 24 AM" src="https://github.com/user-attachments/assets/70518ebf-fbaf-4f25-8fe4-0396f34abf6d" />

## Tworzenie manifestów yaml:
<img width="516" height="200" alt="Zrzut ekranu 2025-12-16 o 1 51 41 AM" src="https://github.com/user-attachments/assets/52ca2367-70d4-4d0b-879a-eefddce1e793" />

## Applying manifestów:

<img width="711" height="611" alt="Zrzut ekranu 2025-12-16 o 1 51 55 AM" src="https://github.com/user-attachments/assets/44f3e83f-a4d1-4a53-9904-55f579ad5503" />

<img width="1258" height="818" alt="Zrzut ekranu 2025-12-16 o 1 52 11 AM" src="https://github.com/user-attachments/assets/c403396a-63db-4c5c-bc50-2dec268fd402" />

## Tworzenie podów testowych:

<img width="379" height="97" alt="Zrzut ekranu 2025-12-16 o 1 52 44 AM" src="https://github.com/user-attachments/assets/b6f4f7c9-ead3-4ae1-9693-3c2d005fc6a3" />

<img width="1005" height="493" alt="Zrzut ekranu 2025-12-16 o 1 53 03 AM" src="https://github.com/user-attachments/assets/c9c35014-31fc-46af-bb19-a1a2f85a50c7" />

## Weryfikacja Network Policies:

<img width="1083" height="541" alt="Zrzut ekranu 2025-12-16 o 1 53 21 AM" src="https://github.com/user-attachments/assets/375c33de-52db-4fcf-88d5-29138f7b0943" />

## Obciążenie:

<img width="608" height="108" alt="Zrzut ekranu 2025-12-16 o 1 53 50 AM" src="https://github.com/user-attachments/assets/f3365d92-e69d-4470-8bac-032e905bec7f" />

<img width="462" height="161" alt="Zrzut ekranu 2025-12-16 o 1 54 20 AM" src="https://github.com/user-attachments/assets/73a46b6a-c52d-4163-8eb1-f91883feff35" />


<img width="959" height="306" alt="Zrzut ekranu 2025-12-16 o 1 53 35 AM" src="https://github.com/user-attachments/assets/f6ec3f2e-2f98-48ca-abb6-4405ecb643ca" />


# ----- Część nieobowiązkowa -----
### 1. TAK, możliwe jest dokonanie aktualizacji aplikacji frontend, co potwierdza dokumentacja: 
https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#migrating-deployments-and-statefulsets-to-horizontal-autoscaling

### 2. Dobór parametrów strategii RollingUpdate

Ze względu na ograniczenia **ResourceQuota** limit CPU: 1000m oraz zapotrzebowanie pojedynczego poda 200m, maksymalna liczba podów w przestrzeni `frontend` to **5**. Dlatego dla HPA należy:
### a)
* **`maxUnavailable: 1`**
    Realizuje utrzymanie **minimum 2 aktywnych Podów**.
    *Uzasadnienie:* Przy minimalnej liczbie replik z HPA, zezwolenie na niedostępność jednej gwarantuje, że dwie pozostaną aktywne.
### b)
* **`maxSurge: 0`**
    Gwarantuje **nieprzekroczenie limitów ResourceQuota**.
  Przy maksymalnym wyskalowaniu limit zasobów jest w pełni wykorzystany. Ustawienie 0 wymusza terminację starego poda przed utworzeniem nowego, zapobiegając błędom `Forbidden`, brak wolnego CPU.

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1  # Gwarancja min. 2 aktywnych podów
      maxSurge: 0        # Ochrona przed przekroczeniem ResourceQuota
```

### c) Korelacja ustawień autoskalera HPA

Nie wprowadzono zmian w ustawieniach HPA, parametry minReplicas: 3 oraz maxReplicas: 5 pozostają bez zmian.

Obecna konfiguracja HPA jest zgodna z przyjętą strategią aktualizacji. maxSurge: 0 w deploymentcie zapewnia bezpieczeństwo zasobów nawet przy maksymalnym wyskalowaniu do 5 replik.
