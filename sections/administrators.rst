********************************
Działania administratora systemu
********************************

Niniejsza część opisuje niektóre działania administracyjne odnoszące się zarówno do samego oprogramowania Mailman, ale także - jeżeli to konieczne - konserwacyjnych prac związanych z serwerem pocztowym, serwerem WWW i systemem operacyjnym w ogóle.

Utworzenie listy dyskusyjnej
****************************

 .. note::
    Do wykonania tej operacji wymagana jest znajomość hasła zarządzania listą dyskusyjną (zob. :ref:`passwords`). Przez przejściem do wykonania przedstawionych czynności - upewnij się, że nim dysponujesz.

Aby dodać nową listę dyskusyjną przejdź do `formularza dodawania list dyskusyjnych <https://listy.siecobywatelska.pl/create>`_, który jest dostępny po przejściu z wykazu stron informacyjnych po wybraniu głównej strony administracyjnej, a następnie linku odnoszącego się do zakładania nowych list dyskusyjnych.

Możesz utworzyć nową listę wpisując niezbędne informacje w formularzu. Nazwa listy będzie występowała w adresie e-mail służącym do wysyłania wiadomości, więc jej nazwa powinna być wpisana małymi literami. Zmiana tej nazwy po założeniu listy jest niemożliwa.

Musisz podać też adres administratora listy. Po jej założeniu, administrator otrzyma potwierdzający e-mail wraz z początkowym hasłem. Administrator listy będzie mógł je zmienić, a także dodawać i usuwać dodatkowych administratorów. Adres ten będzie publicznie dostępny jako adres opiekuna listy dyskusyjnej.

Jeśli chcesz aby Mailman wygenerował hasło automatycznie kliknij "Tak" w odpowiednim polu poniżej i pozostaw pola na hasło puste.

Musisz mieć odpowiednie uprawnienia aby założyć nową listę. Każdy serwer powinien mieć hasło osoby uprawnionej do tworzenia nowych list, które wprowadza się w polu na dole strony. Można też użyć hasła.

W zdecydowanej większości przypadków na pytanie "Czy wstrzymywać do moderowania wiadomości od nowo zapisanych osób?" chcesz odpowiedzieć "Nie". Jest to zachowanie typowe dla list dyskusyjnych, gdzie wpisy nie muszą być akceptowane przez administratora przed skierowaniem ich do pozostałych odbiorców. W innym przypadku każdy wpis będzie wymagał zaakceptowania przez opiekuna listy. Ustawienie to może zostać zmienione.

Po wypełnieniu formularza przejdź do sekcji administracyjnej listy. Możesz to zrobić poprzez odnośnik na ekranie potwierdzenia, albo poprzez odnośnik zawarty w wiadomości wysłanej do administratora listy.

Na stronie głównej sekcji administracyjnej listy odnajdź pole "Zwięzły opis listy" i wypełnij je, aby odzwierciedlał on przeznaczenie danej listy. Zdefiniujesz w ten sposób przedmiot listy, a także usprawnisz np. wygaszenie archiwalnych list.

.. figure:: ../_images/admin-description-field.png

    Lokalizacja pola "Zwięzły opis listy" w głównej sekcji administracyjnej listy

.. TIP::
   Sugeruje się budowanie opisu list dyskusyjnych poprzez dokończenie zdania "Jest to lista dyskusyjna ...". Przykładowa "Jest to lista dyskusyjna... ekspercka na temat sytuacji prawa człowieka do informacji".

W przeciętnej wykorzystaniu listy dyskusyjnej nie zachodzi konieczność wprowadzania dodatkowych zmian w ustawieniach, gdyż optymalne są ustalane ustawienia przez administratora systemu.

Po wprowadzeniu zmian w niniejszej sekcji zapisz je naciskając przycisk "Zatwierdź". Następnie dodaj się do listy dyskusyjnej zgodnie z procedurą :ref:`admin_signup`.

Teraz możesz sprawdzić działanie poprzez przesłanie wiadomości na adres listy dyskusyjnej. Po minucie lub dwóch treść powinna się pojawić w :ref:`archive` .

Zmiana hasła systemowego i hasła tworzenia list
***********************************************

Do ustawienia hasła administratora serwera wykorzystaj polecenie konsoli: ``mmsitepass <twoje-haslo>``

Do ustawienia hasła do tworzenia list wykorzystaj polecenie konsoli: ``mmsitepass -c <twoje-haslo>``

.. note::
    Powyższe polecenia mogą być wykonane tylko przez użytkowników grupy systemu operacyjnego ``list``.


Statystyki
**********

Generowane są statystyki serwera pocztowego i ruchu WWW w celu diagnozowania nadużyć itp. Dostępne są one pod adresem ``https://listy.siecobywatelska.pl/awstats``. W celu zarządzaniem uprawnieniami dostępu wykorzystaj polecenie ``htpasswd`` wobec ``/etc/awstats/htpasswd``. Jest dostępna informacja m. in. na temat liczby przesłanych wiadomości i ich rozmiaru:

.. figure:: ../_images/awstats-daily.png

    Statystyki przesłanych wiadomości w okresie tygodnia

Możliwe jest także wykorzystanie prostych skryptów do sporządzenia statystyk. 

Najpopularniejsi członkowie: ``list_lists -b | while read LINE; do list_members $LINE; done | sort -n | uniq -c | sort -n | more``

Łączna liczba członków: ``list_lists -b | while read LINE; do list_members $LINE; done | wc -l``

Liczba członków na poszczególnych listach: ``list_lists -b | while read LINE; do echo "$LINE;$(list_members $LINE | wc -l)"; done;``

Usuwanie listy dyskusyjnej
**************************

W celu usunięcia listy dyskusyjnej wykorzystaj polecenie ``rmlist``. Szczegółowa instrukcja użytkowania jest dostępna po wykonaniu ``rmlist --help``. W przypadku zachowania archiwum jest ono dostępne w katalogu ``rita.jawne.info.pl:/var/lib/mailman/archives/private/``.

.. note::
    Powyższe polecenia mogą być wykonane tylko przez użytkowników grupy systemu operacyjnego ``list``.

Kopie bezpieczeństwa
********************

Kopie zapasowe są wykonywane codziennie z wykorzystaniem oprogramowania ``duply`` (por. ``rita.jawne.info.pl:/etc/cron.d/duply``) na zewnętrzny serwer (por. ``rita.jawne.info.pl:/root/.duply/main/*``) zgodnie z polityką przyjętą przez Stowarzyszenie.

Monitoring
**********

Codziennie jest dokonywany przegląd dziennika serwera pocztowego w celu analizy trwałych odrzuceń wiadomości (hard bounces). Raport w tym zakresie jest przekazywany do administratora systemu. Przegląd wykonywany jest przez skrypt w ``/etc/cron.daily/bounces_report``.

Trwale są gromadzone metryki z działania całego systemu operacyjnego z wykorzystaniem oprogramowaniem Zabbix.

Masowa aktualizacja ustawień
****************************

W celu dokonania masowej aktualizacji określonych ustawień wszystkich list dyskusyjnych należy wykorzystać narzędzie ``config_list``.

Pierw należy zidentyfikować odpowiednią wartość poprzez analizę ``config_list -o - TEST``.

Następnie należy dokonać modyfikacji wartości z użyciem następującego kodu:

.. code-block:: bash

    list_lists -b | while read LIST; do
        echo "# -*- coding: utf-8 -*-
    variable = 'value'
    " > settings.py;
        config_list -i settings.py "$LIST";
        echo "$LIST updated";
    done;

Masowa aktualizacj języka użytkowników
**************************************

W celu dokonania masowej aktualizacji języka uczestników danej listy dyskusyjnej należy wykorzystać narzędzie ``withlist`` oraz odpowiedni kod Python, który dokona modyfikacji parametrów wszystkich użytkowników. 

Należy w pliku ``member_language.py`` zapisać treść:

.. code-block:: python

    def member_language(m):
        for member in m.members:
            m.setMemberLanguage(member, 'pl')
        m.Save()

Następnie wywołać go z uzpełnieniem ``PYTHONPATH`` o ścieżkę do w/w pliku. Przykładowo ``PYTHONPATH="$(pwd)" python /usr/sbin/withlist -l  -r member_language "$LIST"``.

Aktualizacja e-maila
********************

W celu zaktualizowania adresu e-mail wykonaj:

.. code-block:: bash
   
   $ clone_member -r [stary_adres] [nowy_adres]
   
