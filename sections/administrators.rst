********************************
Działania administratora systemu
********************************

Niniejsza część opisuje niektóre działania administracyjne odnoszące się zarówno do samego oprogramowania Mailman, ale także - jeżeli to konieczne - konserwacyjnych prac związanych z serwerem pocztowym, serwerem WWW i systemem operacyjnym w ogóle.

Zmiana hasła systemowego i tworzenia list
*****************************************

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
