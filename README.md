# M122

## Backup Automatisierung
### Muss Kriterien
- Backup kann auf drei verschieden Arten durchgeführt werden
- Speicherungspfad kann ausgewählt werden
- Backupdateipfad kann selber ausgewählt werden

### Kann Kriterien
- Restore kann gemacht werden
- Info Page

## Betriebsdokumentation
### Installationsanleitung für Administratoren
1. Ubuntu Installieren
2. script auf den Desktop ziehen
3. Terminal öffnen
4. cd Desktop (oder dort wo das Script abgelegt wurde)
5. sudo chmod +x script.sh
6. sudo ./script.sh ausführen

### Bedienungsanleitung für Benutzer
1. Terminal öffnen
2. cd Desktop (oder dort wo das Script abgelegt wurde)
3. sudo ./script.sh ausführen
4. Auswahl zwischen den Backupmöglichkeiten
5. Source Pfadangabe
6. Destination Pfadangabe
7. Backup wird gemacht!


## Das Script
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               backup.sh                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
#!/usr/bin/bash

#-------------------------------Autor-------------------------------------------------
# Cyrill Kälin

#------------------------------Legende
#1 -> Fullbackup
#2 -> incremental
#3 -> differential (noch nicht fertig)
#4 -> Restore
#5 -> TGZ File Reader




#--------------------------------Lexikon-----------------------------------------------
#tar = Tape Archive tool for compression
#tar -cvzpf /{absolute path to destination folder}/filename.tar /{absolute path of files to be compressed}/
#-c     Creates a new archive
#-v     Displays the steps involved in archiving
#-z     Compresses or decompresses the archive directly with gzip
#-p     Maintains access privileges while extracting
#-f     Writes an archive in the given file or reads the data out from the given file

##Check the tgz file
#tar -tzvf /mnt/nfsmount/*.tgz






echo "Was für ein backup möchten Sie machen? 1 = Fullbackup | 2 = Incremental | 4 = Restore | 5 = Check tgz File | ? = Info Sheet"
read auswahl

#Fullbackup
        if [[ $auswahl == "1" ]]; then
                echo "full backup wird ausgeführt"


                #Which date
                DATE=$(date +%Y-%m-%d-%H%M%S)

                #What do you want to backup
                echo "Von Welchen Ordner möchtest du ein Backup machen (zb. /home/user/Desktop)"
                read SOURCE

                #The Folder to be backed up
                echo "In welchen Ordner möchtest du das Backup speichern (ich empfehle: /mnt/nfsmount)"
                read BACKUP_DIR


        #Backup command
        tar -cvzpf $BACKUP_DIR/backup-$DATE.tar.gz $SOURCE

                #Print start status message.
                echo "Backing up" $BACKUP_DIR "to" $Source
                date
                echo

                # Print end status message.
                echo
                echo "Backup finished"
                date

#Incremental
        elif [[ $auswahl == "2" ]]; then


                #What do you want to backup
                echo "Von Welchen Ordner möchtest du ein Backup machen (zb. /home/user/Desktop)"
                read source

                #The Folder to be backed up
                echo "In welchen Ordner möchtest du das Backup speichern (ich empfehle: /mnt/nfsmount)"
                read dest 


                echo "Incremental backup wird ausgeführt"

	##Überprüfen, mit find und alle Dateien im Quellverzeichnis zu finden und mit printf %Pin wird ausgegebenwelches Datum die Dateien haben
                for file in $(find $source -printf "%PIn") ; do

        ##Überprüfen, ob das file im source ordner existiert
                if [ -a $dest/$file ] ; then



        ##Überprüfen, ob das File im source ordner neuer ist als im Dest Ordner
                if [ $source/$file -nt $dest/$file ] ; then
                 echo "Newer file detected, copying..."



        ##Hier wird allles vom Source Ordner in den Dest Ordner kopiert.
                sudo cp -r $source/$file $dest/$file
                else



        ##Wenn nicht wird das alles übersprungen und das file wird direkt nach dest kopiert.
                echo "File $file exists, skipping."
                fi
                else
                echo "$file is being copied over to $dest"
                scp -r $source/$file $dest/$file
                fi
                done

#Restore
        elif [[ $auswahl == "4" ]]; then

        #ins Root wechseln

cd/

                #pasende Infos ausfindig machen
                echo "wo liegt dein Backup file (zb. /home/user/Desktop)"
                read RESTORE_PATH

                #File Name
                echo "Wie heisst dein Backupfile, welches du wiederherstellen möchtest?  (zb. backup-2022-10-31-193414.tar.gz)"
                read RESTORE_FILE

        #Restore Command (Automatisch ins Verzeichnis)
        sudo tar -xzvf $RESTORE_PATH/$RESTORE_FILE

                #End Message
                echo "Der Restore war Erfolgreich"


#Reader
        elif [[ $auswahl == "5" ]]; then

                #Informationen ausfindig machen
                echo "In welchen Verzeichnis befindet sich dein .tgz file (zb. /home/user/Desktop)"
                read PFAD

                echo "Wie heisst dein File? (zb. backup-2022-10-31-193414.tar.gz)"
                read FILE

                #Output message
                echo "Das ist in deinem File"
        #Read Command
        tar -tzvf $PFAD/$FILE

                #End Message
                echo "Fertig tada"

#Info Sheet

        elif [[ $auswahl == "?" ]]; then

        #Info Sheet
        "Was ist TGZ?"
        "TGZ ist ein Programm, welches Ordner Komprimiert, vergleichbar mit 7Zip in Windows"

        "1 = Fullbackup -> Kopieren des ganzen Ordners + Kompriemieren"

        "2 = Differential -> Nur die änderung werden gespeichert (Bausteine)"

        "4 = Restore -> Wenn die Daten aus dem Backup zurückgespielt werden sollen"

        "5 = TGZ Reader -> Wenn man wissen möchte, was im TGZ ""Backup"""



fi




