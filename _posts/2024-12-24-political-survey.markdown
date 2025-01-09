---
layout: post
title:  Political Survey
categories: code
comments: true
excerpt: "C++ survey program for guessing the user's political party using AI" 
---
This was a fun project I completed for the Artificial Intelligence course on study.com as a transfer course for WGU. This is one of my earliest projects, so it could be much cleaner, but I am still proud of it and I think it is entertaining as well.

This application has two main parts - a survey which collects data and a game which guesses the user's political party affiliation in as few questions as possible. The survey portion asks the user 10 political questions and then asks with which political party they most closely align. This data is then used to update a file which records the probability of each party to answer individual questions a certain way. The party guessing game makes use of this historical data by updating party affiliation probability with each question answered and returns a guess when the probability threshold is reached.
<br>


<h2>Languages</h2>

- C++

<h2>Concepts</h2>

- Bayes' Theorem

<h2>Source</h2>
{% highlight ruby %}

#include <iostream>
#include <fstream>
#include <array>    // included only necessary header files for io, array, and filestreaming
using namespace std; // to avoid using the "std::" prefix repeatedly

float polSpect[4]{ 0, 0, 0, 0 }; // a global array representing the political spectrum separated into right, 
                                    // right-center, left-center, and left
void scale10(int userInput) {
    switch (userInput) {  //created a function to scale answers from 1 to 10 to avoid repetitive code
    case 1:
        polSpect[0] += 1;
        break;
    case 2:
        polSpect[0] += .75;
        polSpect[1] += .25;
        break;
    case 3:
        polSpect[0] += .5;
        polSpect[1] += .5;
        break;
    case 4:
        polSpect[0] += .25;
        polSpect[1] += .75;
        break;
    case 5:
        polSpect[1] += 1;
        break;
    case 6:
        polSpect[2] += 1;
        break;
    case 7:
        polSpect[2] += .75;
        polSpect[3] += .25;
        break;
    case 8:
        polSpect[2] += .5;
        polSpect[3] += .5;
        break;
    case 9:
        polSpect[2] += .25;
        polSpect[3] += .75;
        break;
    case 10:
        polSpect[3] += 1;
        break;
    }
};

void scale4(int userInput) {  // same as the scale10 function, but for questions with only 4 answers
    switch (userInput) {
    case 1:
        polSpect[0] += .6F;
        polSpect[1] += .2F;
        polSpect[2] += .1F;
        polSpect[3] += .1F;
        break;
    case 2:
        polSpect[0] += .2F;
        polSpect[1] += .6F;
        polSpect[2] += .1F;
        polSpect[3] += .1F;
        break;
    case 3:
        polSpect[0] += .1F;
        polSpect[1] += .1F;
        polSpect[2] += .6F;
        polSpect[3] += .2F;
        break;
    case 4:
        polSpect[0] += .1F;
        polSpect[1] += .1F;
        polSpect[2] += .2F;
        polSpect[3] += .6F;
        break;
    }
};

string questionList[10]{  // an array to store questions for both survey and guess functions
    "How should the US handle immigration ? \n\n"
    "1. People should be free to come and go as they please without government interference\n"
    "2. Immigration should be strictly regulated\n"
    "3. Immigration should be regulated, but not difficult\n"
    "4. Immigrants should be welcomed and have immediate access to public benefits\n\n",

    "What is your opinion on gun laws?\n\n"
    "1. All gun laws are unconstitutional\n"
    "2. Some reasonable laws are acceptable\n"
    "3. Guns should be strictly regulated\n"
    "4. Guns should be banned\n\n",

    "What is your opinion on world trade?\n\n"
    "1. All countries should trade freely\n"
    "2. Domestic corporations should be protected\n"
    "3. Domestic workers should be protected\n"
    "4. Workers all around the world should be protected\n\n",

    "Should the government have access to private electronic communications?\n\n"
    "1. Never\n"
    "2. With the appropriate warrant\n"
    "3. It's ok if they're terrorists\n"
    "4. I don't have anything to hide\n\n",

    "How should taxation function?\n\n"
    "1. Taxation is theft\n"
    "2. Corporations should not pay taxes\n"
    "3. Corporations should pay most of the taxes\n"
    "4. Tax everybody for everything\n\n",

    "How do you feel about gay marriage?\n\n"
    "1. Marriage is a religious ceremony and the government should not be involved either way\n"
    "2. It should be banned\n"
    "3. It should not be illegal\n"
    "4. Churches should be forced to perform services for any couple requesting them\n\n",

    "How do you feel about crime?\n\n"
    "1. Victimless crimes should not be punished\n"
    "2. Punishment for crimes should always be enforced\n"
    "3. Most criminals can be rehabilitated except in cases such as murder\n"
    "4. All criminals can be rehabilitated, including murderers\n\n",

    "On a scale of 1 to 10, how important are public benefit programs?\n\n",

    "On a scale of 1 to 10, how important is protecting the environment?\n\n",

    "On a scale of 1 to 10, how important is government in daily life?\n\n",
};

void saveData(string fileName) { // function for merging new survey data with existing data in file
    int tempArray[4]{ 0,0,0,0 };
    int tempInt(0); // temporary value to store from survey count file

    ifstream statsLoad;
    statsLoad.open(fileName); // opens text file for reading

    for (int i = 0; i < 4; i++) {
        statsLoad >> tempArray[i]; // loads values into a temporary array
    }
    statsLoad.close();

    ofstream statsWrite;
    statsWrite.open(fileName); // opens text file for writing

    for (int i = 0; i < 4; i++) {
        statsWrite << ((polSpect[i] + tempArray[i]) / 2) << " "; // averages current survey values with
        // stored values and writes to file
    }
    statsWrite.close(); // the following keeps track of how many surveys have been performed
    ifstream surveyNo;
    surveyNo.open("surveycount.txt");
    surveyNo >> tempInt;
    surveyNo.close();

    ofstream noWrite;
    tempInt++;  // increments survey count
    noWrite.open("surveycount.txt"); // writes new survey count
    noWrite << tempInt;
    noWrite.close();
};

void survey() {  // the main survey function for collecting and storing data based on political party

    int answer;
    for (int i = 0; i < 7; i++) {
        cout << questionList[i];
        cin >> answer;
        scale4(answer);
        system("cls");
    }
    for (int i = 7; i < 10; i++) {
        cout << questionList[i];
        cin >> answer;
        scale10(answer);
        system("cls");
    }
    int answer11;
    cout << "With which political party do you most closely align?\n\n";
    cout << "1. Libertarian\n";
    cout << "2. Republican\n";
    cout << "3. Democrat\n";
    cout << "4. Green\n\n";
    cin >> answer11;
    system("cls");

    int tempArray[4]{ 0,0,0,0 }; // temporary array to store values from text file

    switch (answer11) {
    case 1: {
        saveData("libstats.txt");  // saves data to a file
        break;
    }
    case 2:{
        saveData("repstats.txt");
        break;
    }
    case 3: {
        saveData("demstats.txt");
        break;
    }
    case 4: {
        saveData("grnstats.txt");
        break;
    }
}
};

void guess(){
    float libStats[4]{ 0,0,0,0 }; // local arrays to store from saved data for comparison
    float repStats[4]{ 0,0,0,0 };
    float demStats[4]{ 0,0,0,0 };
    float grnStats[4]{ 0,0,0,0 };
    float bayesMatrix[4][3]{        // for updating user guess probabilities
    {.25 /*inital probability, updated later*/, 0 /*polSpect*/, 0 /*product of c0 and c1*/}, // libertarian
    {.25 /*inital probability, updated later*/, 0 /*polSpect*/, 0 /*product of c0 and c1*/}, // republican
    {.25 /*inital probability, updated later*/, 0 /*polSpect*/, 0 /*product of c0 and c1*/}, // democrat
    {.25 /*inital probability, updated later*/, 0 /*polSpect*/, 0 /*product of c0 and c1*/} // green
    };

    for (int i = 0; i < 4; i++) { // clears polSpect array
        polSpect[i] = 0;
    }

    ifstream rep;                 // loads stored data into local arrays
    rep.open("repstats.txt");
    for (int i = 0; i < 4; i++) {
        rep >> repStats[i];
    }
    ifstream grn;
    grn.open("grnstats.txt");
    for (int i = 0; i < 4; i++) {
        grn >> grnStats[i];
    }
    ifstream dem;
    dem.open("demstats.txt");
    for (int i = 0; i < 4; i++) {
        dem >> demStats[i];
    }
    ifstream lib;
    lib.open("libstats.txt");
    for (int i = 0; i < 4; i++) {
        lib >> libStats[i];
    }

    float histStats[4]; // loads answers over 20% in history into an array
    for (int i = 0; i < 4; i++) {
        if ((libStats[i] / 10) >= .2) {
            histStats[i] = libStats[i];
        }
        if ((demStats[i] / 10) >= .2) {
            histStats[i] = libStats[i];
        }
        if ((repStats[i] / 10) >= .2) {
            histStats[i] = libStats[i];
        }
        if ((grnStats[i] / 10) >= .2) {
            histStats[i] = libStats[i];
        }
    }

    int answer;
    float probSum(0);
    for (int i = 0; i < 7; i++) { // this loop starts the first round of questions
        cout << questionList[i];
        cin >> answer;
        scale4(answer);
        for (int j = 0; j < 4; j++) { // this loop performs all the bayes theorem calculations
            bayesMatrix[j][1] = polSpect[j] / 10;
            bayesMatrix[j][2] = (bayesMatrix[j][0] * bayesMatrix[j][1]);
            probSum = (probSum + bayesMatrix[j][2]);
            bayesMatrix[j][0] = bayesMatrix[j][2] / probSum; // replaces initial probability with posterior
        }
        system("cls");
       for (int i = 0; i < 4; i++) { // this loop compares current user answers to historical data
            if (bayesMatrix[i][1] >= .2) { // if the user's answers are over 20% in one category,
                if (i == 0) {              // this loop will determine which party also answered over 20%
                    system("cls");
                    cout << "My guess is that you are a Libertarian." << endl;
                    system("pause");
                    system("cls");
                    return;
                    break;
                }
            }
            if (bayesMatrix[i][1] >= .2) {
                if (i == 1) {
                    system("cls");
                    cout << "My guess is that you are a Republican." << endl;
                    system("pause");
                    system("cls");
                    return;
                    break;
                }
            }
            if (bayesMatrix[i][1] >= .2) {
                if (i == 2) {
                    system("cls");
                    cout << "My guess is that you are a Democrat." << endl;
                    system("pause");
                    system("cls");
                    return;
                    break;
                }
            }
            if (bayesMatrix[i][1] >= .2) {
                if (i == 3) {
                    system("cls");
                    cout << "My guess is that you are a Green." << endl;
                    system("pause");
                    system("cls");
                    return;
                    break;
                }
            }
        }
    }
    for (int i = 7; i < 10; i++) { // second round of questions (which should not be needed)
        cout << questionList[i];
        cin >> answer;
        scale10(answer);
        for (int j = 0; j < 4; j++) {
            bayesMatrix[j][1] = polSpect[j] / 10;
            bayesMatrix[j][2] = (bayesMatrix[j][0] * bayesMatrix[j][1]);
            probSum = (probSum + bayesMatrix[j][2]);
            bayesMatrix[j][0] = bayesMatrix[j][2] / probSum;
        }
        for (int i = 0; i < 4; i++) {
            if (bayesMatrix[i][1] >= .2) {
                if (i == 0) {
                    system("cls");
                    cout << "My guess is that you are a Libertarian." << endl;
                    system("pause");
                    system("cls");
                    return;
                    break;
                }
            }
            if (bayesMatrix[i][1] >= .2) {
                if (i == 1) {
                    system("cls");
                    cout << "My guess is that you are a Republican." << endl;
                    system("pause");
                    system("cls");
                    return;
                    break;
                }
            }
            if (bayesMatrix[i][1] >= .2) {
                if (i == 2) {
                    system("cls");
                    cout << "My guess is that you are a Democrat." << endl;
                    system("pause");
                    system("cls");
                    return;
                    break;
                }
            }
            if (bayesMatrix[i][1] >= .2) {
                if (i == 3) {
                    system("cls");
                    cout << "My guess is that you are a Green." << endl;
                    system("pause");
                    system("cls");
                    return;
                    break;
                }
            }
        }
    }
};

void stats() {
    float repStats[4]{ 0,0,0,0 }; // created this function just as a quick way to view stats instead of txt files
    float libStats[4]{ 0,0,0,0 };
    float demStats[4]{ 0,0,0,0 };
    float grnStats[4]{ 0,0,0,0 };

    for (int i = 0; i < 4; i++) { 
        polSpect[i] = 0;
    }

    ifstream rep;                 
    rep.open("repstats.txt");
    cout << "Republican - ";
    for (int i = 0; i < 4; i++) {
        rep >> repStats[i];
        cout << repStats[i] * 10 << "% ";
    }
    rep.close();

    cout << endl << "Green - ";
    ifstream grn;
    grn.open("grnstats.txt");
    for (int i = 0; i < 4; i++) {
        grn >> grnStats[i];
        cout << grnStats[i] * 10 << "% ";
    }
    grn.close();

    cout << endl << "Democrat - ";
    ifstream dem;
    dem.open("demstats.txt");
    for (int i = 0; i < 4; i++) {
        dem >> demStats[i];
        cout << demStats[i] * 10 << "% ";
    }
    dem.close();

    cout << endl << "Libertarian - ";
    ifstream lib;
    lib.open("libstats.txt");
    for (int i = 0; i < 4; i++) {
        lib >> libStats[i];
        cout << libStats[i] * 10 << "% ";
    }
    lib.close();
    
    ifstream surNo;
    int howMany(0);
    surNo.open("surveycount.txt");
    surNo >> howMany;
    cout << "\n\n" << howMany << " surveys performed\n\n";
    cout << system("pause");
    cout << system("cls");
};

int main(){
    int userSelection;
    cout << "Press 1 to take a survey\n \nPress 2 to guess political party\n \nPress 3 to show saved stats\n"
        "\nPress 4 to exit\n\n";
    cin >> userSelection;
    switch (userSelection){
    case 1:{
        system("cls");
        survey();
        break;
    }
    case 2: {
        system("cls");
        guess();
        break;
    }
    case 3: {
        system("cls");
        stats();
        break;
    }
    case 4: {
        cout << "Goodbye!";
        exit(0);
    }
}
     main();
};

{% endhighlight %}

{% if page.comments %} 

<br><br>
<p>
<div id="disqus_thread"></div>
<script>
    (function() { 
        var d = document, s = d.createElement('script');
        
        s.src = 'https://tlkroll.disqus.com/embed.js';
        
        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
</p>

{% endif %} 