Oto kroki, które możesz podjąć, aby skonfigurować klucz SSH i połączyć się z serwerem za pomocą GitHub Actions:

1. **Generowanie klucza SSH:**

   - Lokalnie w terminalu wykonaj polecenie:
     ```bash
     ssh-keygen -t rsa -b 4096 -f ~/.ssh/github_actions_key -C "your_email@example.com"
     ```
   - Zostaniesz poproszony o podanie hasła. Możesz zostawić je puste, ale zaleca się dodanie hasła dla dodatkowej warstwy zabezpieczeń.

2. **Dodanie klucza publicznego do serwera:**

   - Skopiuj klucz publiczny na serwer za pomocą polecenia:
     ```bash
     ssh-copy-id -i ~/.ssh/github_actions_key user@your_server_ip
     ```
   - Wprowadź hasło użytkownika na serwerze.

3. **Dodanie klucza prywatnego do tajemnic w GitHub Actions:**

   - Skopiuj zawartość klucza prywatnego do schowka.
   - W repozytorium na GitHub przejdź do ustawień (Settings) > Secrets > New repository secret.
   - Utwórz nową tajemnicę, np. o nazwie `SSH_PRIVATE_KEY`, i wklej klucz prywatny jako wartość.

4. **Przygotowanie pliku konfiguracyjnego GitHub Actions:**

   - Dodaj plik `.github/workflows/deploy.yml` z następującą zawartością:

     ```yaml
     name: Deploy to Server

     on:
       push:
         branches:
           - main

     jobs:
       deploy:
         runs-on: ubuntu-latest

         steps:
           - name: Checkout repository
             uses: actions/checkout@v2

           - name: Set up SSH
             uses: webfactory/ssh-agent@v0.5.4
             with:
               ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}

           - name: Deploy to Server
             run: |
               # Tutaj możesz umieścić polecenia do kopiowania plików, uruchamiania skryptów itp.
               scp -r ./your_directory user@your_server_ip:/path/to/destination
             # Dodaj kolejne polecenia, jeśli potrzebujesz więcej działań.
     ```

5. **Zmienne środowiskowe w pliku konfiguracyjnym:**

   - Jeśli potrzebujesz przekazać dodatkowe informacje, takie jak ścieżki, zmienne środowiskowe itp., możesz je ustawić jako zmienną środowiskową w pliku konfiguracyjnym GitHub Actions.

6. **Uruchomienie workflow:**
   - Po zatwierdzeniu pliku `.github/workflows/deploy.yml` i wrzuceniu go na główną gałąź repozytorium, GitHub Actions powinien uruchomić workflow automatycznie po każdym `push` do gałęzi `main`.

Upewnij się, że dostosowujesz ścieżki, nazwy użytkowników, adresy IP i nazwy kluczy do swojego konkretnego przypadku. Pamiętaj, aby dokładnie przetestować konfigurację na serwerze testowym przed wdrożeniem na serwer produkcyjny.
