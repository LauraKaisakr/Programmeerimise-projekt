```python
from datetime import datetime


def küsi_toiduained():
    #Küsib kasutajalt toiduained ja nende aegumiskuupäevad.
    toidud = []

    print("\nSisesta külmkapis olevad toiduained.")
    print("Kui kõik on sisestatud, kirjuta toiduaineks 'valmis'.")

    while True:
        nimi = input("\nSisesta toiduaine: ")

        if nimi.lower() == "valmis":
            break

        kuupäev = input("Sisesta aegumiskuupäev (päev.kuu.aasta): ")

        toidud.append({"nimi": nimi, "kuupäev": kuupäev})

    return toidud


def salvesta_faili(toidud):
    #Salvestab toiduained faili.
    fail = open("toidud.txt", "w", encoding="utf-8")

    for toit in toidud:
        fail.write(toit["nimi"] + " " + toit["kuupäev"] + "\n")

    fail.close()


def loe_failist():
    #Loeb toiduained failist ja muudab kuupäevad kuupäevaobjektideks.
    toidud = []

    fail = open("toidud.txt", "r", encoding="utf-8")

    for rida in fail:
        osad = rida.strip().split()

        nimi = " ".join(osad[:-1])
        kuupäev = datetime.strptime(osad[-1], "%d.%m.%Y")

        toidud.append({
            "nimi": nimi,
            "kuupäev": kuupäev
        })

    fail.close()

    return toidud


def leia_aegunud(toidud, tänane):
    #Leiab kõik toiduained, mille aegumiskuupäev on möödas.
    aegunud = []

    for toit in toidud:
        if toit["kuupäev"] < tänane:
            aegunud.append(toit)

    return aegunud


def leia_lähimad(toidud, tänane):
    #Leiab toiduained, mille aegumiskuupäev on kõige lähemal.
    tulevased = []

    for toit in toidud:
        if toit["kuupäev"] >= tänane:
            tulevased.append(toit)

    if len(tulevased) == 0:
        return []

    kõige_lähem = tulevased[0]

    for toit in tulevased:
        if toit["kuupäev"] < kõige_lähem["kuupäev"]:
            kõige_lähem = toit

    lähimad = []

    for toit in tulevased:
        if toit["kuupäev"] == kõige_lähem["kuupäev"]:
            lähimad.append(toit)

    return lähimad


def näita_aegunud(aegunud):
    #Näitab kasutajale aegunud toiduained.
    print("\n--- AEGUNUD TOIDUAINED ---")

    if len(aegunud) == 0:
        print("Aegunud toiduaineid ei ole.")
    else:
        for toit in aegunud:
            print(toit["nimi"] + " - VISKA ÄRA!")


def näita_lähimaid(lähimad, tänane):
    #Näitab kõige lähemal aeguvate toiduainete infot.
    if len(lähimad) == 0:
        print("\nKõik toiduained on aegunud.")
        return

    print("\n--- KÕIGE LÄHEMAL AEGUMISELE ---")

    for toit in lähimad:
        päevad = (toit["kuupäev"] - tänane).days

        print(toit["nimi"] + " - aegumiseni " + str(päevad) + " päeva")

    print("\n--- RETSEPTISOOVITUS ---")

    retsepti_toidud = ""

    for toit in lähimad:
        if retsepti_toidud == "":
            retsepti_toidud = toit["nimi"]
        else:
            retsepti_toidud += " " + toit["nimi"]

    print("Proovi retsepti nende toiduainetega:")
    print(retsepti_toidud)

    otsingu_sõna = retsepti_toidud.replace(" ", "%20")

    retsepti_link = ("https://www.google.com/search?q=retsept%20" + otsingu_sõna)

    print("Retseptiotsingu link:")
    print(retsepti_link)


def main():
    #Käivitab Külmkapiabi programmi.
    print("Tere tulemast Külmkapiabisse!")

    kuupäev = input("Sisesta tänane kuupäev (päev.kuu.aasta): ")
    tänane = datetime.strptime(kuupäev, "%d.%m.%Y")

    toidud = küsi_toiduained()

    salvesta_faili(toidud)
    print("\nAndmed on faili salvestatud.")

    toidud_failist = loe_failist()

    aegunud = leia_aegunud(toidud_failist, tänane)
    näita_aegunud(aegunud)

    lähimad = leia_lähimad(toidud_failist, tänane)
    näita_lähimaid(lähimad, tänane)

    print("\nProgramm lõpetas töö.")


main()

```
