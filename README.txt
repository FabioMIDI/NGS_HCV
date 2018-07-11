
###################################### Prerequisiti ################################################

moduli e pacchetti che devono essere installati:
-moduli python
Biopython(Bio package)
numpy
pandas

-pacchetti bash
bwa
mothur
ncbi-blast+

il software controlla in automatico  che tutti i pacchetti necessari per la sua corretta esecuzione siano installati nel sistema linux.
In caso non siano presenti ti avverte e blocca l'esecuzione.

####################################### Come funziona(descrizione argomenti,processamenti,opzioni) ################################################


------- esecuzione script di avvio ------------------------------------------------------
per eseguire il software dal terminale,bisogna eseguire con l'inteprete di python lo script di avvio del software.
il nome dello script di avvio è per il momento NGS_HCV_analysis.py(eventualmente potremmo sostituirlo con il nome vero del software) 
e si trova dentro la cartella del software per il momento chiamata new_NGS_HCV_soft.
Questo script esegue tutti gli scripts che fanno parte della pipeline. 
Questi scripts si trovano nella cartella "scripts" dentro la cartella del software.
Dunque la prima parte del comando del terminale prevede l'esecuzione dello script di avvio

user@user:~$ python percorso_directory/new_NGS_HCV_soft/NGS_HCV_analysis.py ...

il percorso_directory è il percorso assoluto o relativo verso lo script di avvio contenuto nella cartella del software new_NGS_HCV_soft 


------- argomenti fondamentali -------------------------------------------------------------
a questo punto bisogna inserire degli argomenti obbligatori in base a ciò che ti interessa
premessa: all'interno dello script principale è presente una serie di codici che si assicurano che lo user inserisca l'input correttamente
in caso di input errato l'esecuzione viene bloccata e sono printati sul display e riportati nel log_file il motivo dell'input errato.


-R percorso_cartella_reads

NOTA: -R è obbligatorio a prescindere da ciò che fai dopo.

### caso in cui il tuo input sia:
1) un file reads derivato da sequenziamento single end di next generation 
2) un file di reads derivato da sequenziamento pair end con le reads forward e le reads reverse all'interno dello stesso file

in entrambi i casi usi solo l'argomento -r
-r nome_singolo_file_di_reads_da_processare presente all'interno della cartella delle reads

verrà eseguito un single end mapping del file di reads contro la reference

### caso in cui il tuo input sia:
1) due files di reads da processare che devono subire il merging 
usi -rf e -rrv come argomenti al posto di -r
-rf nome_file_reads_forward 

-rrv nome_file_reads_reversed 

2) due files di reads ottenuti da un sequenziamento pair end in cui la forward e la reverse si trovano in due file separati
usi -rf e -rrv come argomenti al posto di -r
e specifichi anche l'argomento -p che sta per pair end mapping

-rf nome_file_reads_forward

-rrv nome_file_reads_reversed

-p 

quest'ultimo argomento serve per dire:
"non eseguire il merging fra le due reads mappando un singolo file ma mappale in contemporanea come coppia sulla reference".

nel caso 1) verrà eseguito sempre un single end mapping contro la reference

nel caso 2) verrà eseguito dunque un pair end mapping contro la reference

devi inoltre indicare il gene dell'amplicone

-gn {"NS3","NS5B","NS5A"} ,fra parentesi graffe ci sono le possibilità fra cui scegliere. 
un input fuori dalle tre possibilità dà errore.

Se lo user mette il gene "errato" delle sue reads. il mapping è zero o cmq basso. NS3,NS5B,NS5A sono molto diversi fra di loro.
Il fatto che il mapping sia basso può essere dovuto comunque a vari motivi.

------- argomenti facoltativi ----------------------------------------------------------------
non sono strettamente necessari per il corretto funzionamento del software ma possono essere utili

-wd nuova_working_directory

wd sta per working_directory
Cambi la working directory in cui il software processa i files temporanei e dove sono riportati i files di output. il default è la cartella dove hai aperto il terminale
devi indicare anche il nuovo percorso delle reads folder se ti sposti  ovviamente)

-btch n 

sta per batching. Puoi scegliere di analizzare n numero di reads( un batch di reads appunto) estratte casualmente dal file invece che tutte le reads. Utile per test se vuoi essere veloce.

-tv n

sta per threshold_value. Puoi decidere di considerare come mutazioni rilevanti che conferiscono resistenza  solo quelle 
con una prevalenza superiore a un certo valore n.

-prc n

sta per processors. Selezioni il numero di processori con cui eseguire il blast,il bwa mapping,e il merging(che sono eseguiti con software esterni) ma non controlla i processori per l'esecuzione della pipeline.

-gt {"1a","1b","2","3","4","unknown"}

sta per genotype. Se conosci il genotipo dei tuoi file di reads puoi indicarlo qui. In caso non specifichi nulla il default è unknown.
in quest'ultimo caso viene eseguito il blast contro un pool di migliaia di sequenze di HCV 
(non contro le references come detto alla riunione,poi l'ho rivisto). Il pool copre i principali genotipi di HCV.
il genotipo con la prevalenza più alta viene assegnato al genotipo delle reads. Vari tests con reads di cui conosciamo il genotipo,
hanno dimostrato che blast assegna sempre correttamente il genotipo alle reads,nonostante nel pool alcuni genotipi siano sotto rappresentati(i 3 e i 4 sono genotipi rarissimi)
Questo modo di procedere risale alla versione fatta con Leo,ed è modificata solo leggermente.
i genotipi indicati fra le possibili scelte sono quelli per cui abbiamo le reference nella cartella del software.

infatti il mapping viene eseguito contro la reference specifica per gene e genotipo delle reads di input.

sia il gene che il genotipo ci servono anche nella fase di comparazione con il rule set non solo per il mapping contro la reference. 
Perchè ogni mutazione rilevante(e drug associata) cambia in base a gene e genotipo.



-usrf  path_user_ref_folder/user_ref.fasta

sta per user_ref. Specificando il percorso completo verso un file fasta,lo user può decidere 
di mappare le reads di input contro la sua reference. Anche qui se non specifa il genotipo, viene assunto unknown, e lo trova il blast.
il mappaggio contro la user reference richiede ulteriore processamenti aggiuntivi e quindi un po' più tempo.
 
gli argomenti(sia fondamentali sia opzionali) possono essere inseriti in qualsiasi ordine.

############################## esempi di esecuzione corretta #####################################
Esempio:
la cartella delle reads si chiama reads_folder e contiene due files di reads reads_1.fastq e reads_2.fastq risultato dell'amplificazione
del gene NS5A e genotipo 1a. Ogni file di reads contiene 1000 reads. 
E' presente anche il file fasta di una reference esterna a quelle del software e si trova nella cartella user_ref_folder. 
La user_reference deve essere in fasta format.
Se i files di input sono compressi con (.gz format) vengono decompressi.
 
hai aperto il terminale in una directory dove si trova la directory delle reads,che qui chiamiamo reads_folder 
La cartella del software con dentro lo script di avvio e tutte le cartelle e files necessari al suo funzionamento si trova nella Scrivania.

le reads di input devono essere in formato .fastq o si blocca l'esecuzione(e viene avvertito lo user del motivo)

- processamento del sam generato da un single end mapping(argomenti base):

python /home/sebastiano/Scrivania/new_NGS_HCV_soft/NGS_HCV_analysis.py -R ./reads_folder -r reads_1.fastq -gn NS5A

questo è il minimo necessario per l'esecuzione se stiamo facendo un single end mapping
sono mappate tutte le reads del file reads_1.fastq, quindi 1000 reads mappate,
contro la reference del gene NS5A e del genotipo trovato dal blast. 
Il genotipo infatti non è stato specificato ed è sconosciuto e viene eseguito il bast per trovarlo.


- processamento del sam generato da un single end mapping:
python /home/sebastiano/Scrivania/new_NGS_HCV_soft/NGS_HCV_analysis.py -R ./reads_folder -r reads_1.fastq -gn NS5A -gt 1a -btch 100

stiamo mappando 100 reads casuali del file di reads reads_1.fastq (abbiamo specificato -btch 100)
contro la reference contenuta dentro il software e specifica del gene NS5A e genotipo 1a 
il blast non è eseguito(abbiamo specificato il genotipo,-gt 1a)

- processamento del sam generato da un single end mapping con reads mergiate(argomenti base):
python /home/sebastiano/Scrivania/new_NGS_HCV_soft/NGS_HCV_analysis.py -R ./reads_folder -rf reads_1.fastq -rrv reads_2.fastq -gn NS5A

questo è il minimo necessario all'esecuzione se stiamo facendo un single end mapping con le reads risultate dal merging.
tutte le reads di ogni file vengo mergiate e otteniamo 1000 reads mergiate che mappiamo 
contro la reference del gene NS5A e genotipo trovato dal blast.

- processamento del sam generato da un pair end mapping(argomenti base):

python /home/sebastiano/Scrivania/new_NGS_HCV_soft/NGS_HCV_analysis.py -R ./reads_folder -p -rf reads_1.fastq -rrv reads_2.fastq -gn NS5A

questo è il minimo necessario all'esecuzione se stiamo facendo un pair end mapping.
Abbiamo specificato -p. Le reads vengono mappate insieme ma separatamente sulla reference del gene NS5A e genotipo trovato dal blast.
quindi in totale mappiamo il doppio delle reads(l'esecuzione del software con il pair end mapping è due volte più lento),ovvero 2000 reads.

- processamento del sam generato da un pair end mapping:
python /home/sebastiano/Scrivania/new_NGS_HCV_soft/NGS_HCV_analysis.py -R ./reads_folder -p -rf reads_1.fastq -rrv reads_2.fastq -gn NS5A -gt 1a e btch 100

anche qui pair end mapping.  Non viene eseguito il bast perchè abbiamo indicato il genotipo.
Mappiamo solo 100 reads casuali per file,quindi mappiamo 200 reads.

- processamento del sam generato da un single end mapping(user_ref):
python /home/sebastiano/Scrivania/new_NGS_HCV_soft/NGS_HCV_analysis.py -R ./reads_folder -r reads_1.fastq -gn NS5A -gt 1a -btch 100 -usrf user_ref_path/user_ref.fasta

in questo caso non mappiamo contro una delle reference contenute nella cartella del software ma contro la reference scelta dallo user.
non eseguiamo il bast,perchè è stato indicato il genotipo. E abbiamo analizzato solo 100 reads casuali del file.


ho usato il termine mappare,però in realtà è un tentativo di mapping,perchè ovviamente alcune o tutte potrebbero non mappare.
Quelle analizzate dal processamento vero e proprio sono quelle mappate correttamente.


############################# output #############################################################

una volta terminato il processamento del sam. A prescindere dalle opzioni scelte ci sarà sempre lo stesso output.

7 files di output:
-df_all_ref_pos_all_aa_prevalence.csv: un gigantesco file excel che riporta le prevalenze degli aminoacidi trovate nelle reads mappate rispetto al totale delle reads mappate
 in base alla posizione. 
Indica anche il coverage per posizione(sia in frequenza relativa che in frequenza assoluta). 
E inoltre indica le prevalenze delle delezioni in base alla posizione delle reads rispetto la reference.

-df_all_ref_pos_aa_ins_prevalence.csv: un altro gigantesco file excel dedicato solo alle inserzioni,che indica la prevalenza degli aminoacidi di inserzione in base alla posizione.
per esempio il 20% di reads a partire dalla posizione x della reference ha inserzioni di S,il 5% ha inserzioni di M ecc..
Indica inoltre la percentuale totale di reads che hanno inserzioni in quella posizione rispetto al totale delle reads(correttamente mappate sulla reference) e la frequenza assoluta.

- df_rule_set: porzione del rule set che riporta le mutazioni rilevanti trovate nelle reads(in base al loro gene e genotipo),le drugs associate,l'effetto (possible resistant o resistant), 
e le reference della letterature scientifica
Se non ci sono mutazione rilevanti nel file di reads di input uscirà il messaggio:
"Warning: no relevant mutations founded in the selected input reads file..."
e alcuni file di output connesse alle mutazioni rilevanti di resistenza ovviamenente non verranno prodotti,vedi dopo.

-df_rules_found_vs_prevalences: contiene le mutazioni rilevanti con le prevalenze associate. 
Se -tv è stato specificato,non verrano considerate quelle sotto il treshold scelto.

-drugs_resistant_and_susceptible: file txt che riporta l'elenco delle drugs contro cui è stata trovata resistenza
(già indicate nel df_rule_set) e l'elenco delle drugs con suscettibilità.

-file sam grezzo

-log_file: riporta dettagliate informazioni su cosa ha fatto il software. 
Riporta sempre il numero di reads totali del file sam ,l'effettivo numero di reads mappate correttamente
(sia numero assoluto che prevalenza rispetto al totale delle reads del file) e quindi analizzate.
riporta inoltre eventuali errori avvenuti durante l'esecuzione del software 
e warning di avviso per l'utente su eventuali aspetti biologici venuti fuori durante l'esecuzione del software.
Esempio ti informa di un basso mapping in caso il numero di reads mappate sia sotto una  certa soglia (50%,scelta arbitrariamente) 
Non elenco tutti i possibili warning ed errori che informano l'utente. Comunque insieme sono più di una ventina sicuro.
sono di vari tipi: alcuni riguardano l'immissione errata di input dello user,altri riguardano alcuni dettagli del processamento
e altri danno informazioni di tipo biologico.
E tutti verranno riportati nel log_file.
Il file è diviso in 4 o 5(con il blast) sezioni, ognuna dedicata a una parte del software.

Nota: si potrebbero fondere alcuni file di output(non tutti) tra di loro al posto di separarli 
ma li ho tenuti separati perchè messi tutti insieme mi sembrava caotico,dato che danno molte informazioni.
 
############################ Note aggiuntive ####################################################


- In questa nuova versione come avete letto ho distinto fra single end mapping e pair end mapping. Perchè effettivamente 
se la tua macchina next generation ti dà una coppia di files e non uno solo,lo user vuole fare un pair end mapping
cioè vuole mapparle insieme,non vuole mappare solo un singolo file di reads separatamente. 
Questo almeno è ciò che ho letto in vari papers sul mapping,e sono le stesse opzioni presenti su bwa.
quindi la possibilità di mappare due files di reads legati fra loro ritengo sia importante. 
il merging si può togliere,per il momento l'ho lasciato come nella versione precedente.

- il processamento è stato fortemente arricchito rispetto alla versione precedente. L'ho riscritto completamente da zero. 
E' la parte su cui ho dedicato la maggior parte del tempo e sui cui pesa putroppo tutto il carico di lavoro. 
La gestione delle indel soprattuto si prende molto tempo ed è in assoluto la parte più complessa del software. 
Sul mio computer(2 core fisici e 4 threads virtuali) impiega circa 1 minuto per analizzare 100 reads(senza blast e processamento della user ref quindi in condizioni ottimali)
un pair end può impiegare 3 minuti per 100 reads.


-Ho testato il software sia su reads nostre sia su reads esterne. Le reads esterne che ho testato le ho scaricate da SRA.
Ho eseguito i tests sia su reads Illumina che su reads pacBio e non ho riscontrato problemi. 
Su pacBio non c'era scritto il gene di amplificazione,quindi non ho capito cosa hanno amplificato. 
In ogni il caso il software non analizza le sequenze di input, le prende e le mappa stop a prescindere da cosa sono . Nel momento in cui le mappa,a meno che il mapping non sia zero,
analizza gli allineamenti e riporta le informazioni finali. Finchè c'è mapping ,anche basso,riporterà l'output finale. 


