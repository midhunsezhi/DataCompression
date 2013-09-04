#include <iostream>
#include <fstream>
#include <sstream>
#include <string>

using namespace std;

//Only the methods and boundary cases of the queue required for this application are implemented.

class myQueue
{
    public:

        int nodes[3][95];
        string code[95];
        int maxSize;
        int front;
        int rear;
        void enqueue(int a);
        int dequeue();
        string compressMessage(string bitstring);
};

void myQueue::enqueue(int a){
    rear=rear+1;
    nodes[1][rear]=a;
}

int myQueue::dequeue(){
    int k= nodes[1][front];
    front=(front+1) % maxSize;
    rear=front-1;
    return k;
}

string myQueue::compressMessage(string bitstring){
string comp="";
unsigned char out=0;
unsigned int count=0;
for(int c=0; c<bitstring.length();c++){
    int bit= bitstring[c]=='0' ? 0x0 : 0x1;

    out |=(bit<<count);

    if(count==8){
        comp+= (char) out;
        //cout<<(char) out;
        count=0;
        out=0;
    }
    count++;

}
comp+=(char)out;
return comp;
}

//main() method

int main(){

//read the .txt file and copy the charecters to string str1

  string line;
  string str1="";
  ifstream myfile ("input.txt");
  if (myfile.is_open()) {
    while ( myfile.good() ) {
      getline (myfile,line);
      str1+=line;
    }
    myfile.close();
  }

  else
    cout << "Unable to open file"<<endl<<"The input file name should be input.txt";

  int frequencies[95];

  for(int i=0;i<95; i++) {
    frequencies[i]=0;
    }


//copy all the characters of str1 whose ascii value is >=32 and smaller than 127 to another string named str and count the frequency of each charecter.


string str="";
for(int i=0;i<str1.size();i++){
    int k= str1[i];
    if (k>=32 && k<127){
        str+=str1[i];
        frequencies[k-32]++;
    }
}


int NonZeroes=0;
for(int i=0;i<95;i++){
    if (frequencies[i] != 0){
        NonZeroes++;
    }
}

//frequency[][] is a 2-d array. The first row contains the ascii values of the characters and the 2nd row contains their respective frequency values

int frequency[2][NonZeroes];
int j=0;

for(int i=0;i<95;i++){
    if(frequencies[i] !=0){
            frequency[0][j]=i+32;
            frequency[1][j]=frequencies[i];
            j++;
    }
}

//sort the second row of the frequency matrix while keeping the "ascii value-frequency" relation between the two rows unchanged.

for(int i=0;i<NonZeroes;i++){
    for(int j=0;j<i;j++){
        if(frequency[1][i]<frequency[1][j]){
            int temp=frequency[1][j];
            frequency[1][j]=frequency[1][i];
            frequency[1][i]=temp;
            temp=frequency[0][j];
            frequency[0][j]=frequency[0][i];
            frequency[0][i]=temp;
        }
    }
}

myQueue Q1, Q2;

 //Q1 deals with the leaves of the tree i.e the characters in the text file.

Q1.front=0;
Q1.rear=-1;
Q1.maxSize= NonZeroes;
Q1.nodes[1][Q1.maxSize]= str.size()+1;
for(int i=0; i<Q1.maxSize;i++){
    Q1.code[i]="";
}

for(int i=0;i<Q1.maxSize; i++){
    Q1.enqueue(frequency[1][i]);
    Q1.nodes[0][i]=frequency[0][i];
}

cout<<endl<<"1st Queue."<<endl;

cout<<"---------------------------------"<<endl;
cout<<"char"<<"\t"<<"frequency"<<endl;
cout<<"---------------------------------"<<endl;

for(int i=0;i<NonZeroes; i++){
    cout<<char(Q1.nodes[0][i])<<"\t"<<Q1.nodes[1][i]<<endl;
}

//Q2 contains the internal nodes of the huffman tree.

Q2.front=0;
Q2.rear=-1;
Q2.maxSize=NonZeroes-1;

for(int i=0; i<Q2.maxSize;i++){
    Q2.code[i]="";
}

Q2.enqueue(Q1.dequeue()+Q1.dequeue());
Q2.nodes[0][0]=1;
Q2.nodes[2][0]=2;

int p=2;
int q=0;
int it=1;
int min1=0;
int min2=0;

while(p<Q1.maxSize || q<Q2.maxSize){
        if(Q1.nodes[1][p]<Q2.nodes[1][q]){
            min1=Q1.dequeue();
            p++;
            Q2.nodes[0][it]=p;
        }else{
            min1=Q2.nodes[1][q];
            q++;
            Q2.nodes[0][it]=-(q);
        }

        if(Q1.nodes[1][p]<Q2.nodes[1][q]){
            min2=Q1.dequeue();
            p++;
            Q2.nodes[2][it]=p;
        }else{
            min2=Q2.nodes[1][q];
            q++;
            Q2.nodes[2][it]=-(q);
        }

        Q2.enqueue(min1+min2);
        it++;
}
cout<< endl<< "2nd Queue."<< endl;
cout<<"---------------------------------"<<endl;
cout<<"LChild"<<"\t\t"<<"Weight"<<"\t\t"<<"RChild"<< endl;
cout<<"---------------------------------"<<endl;

for(int i=0; i<Q2.maxSize; i++){
    cout<<Q2.nodes[0][i]<<"\t\t"<<Q2.nodes[1][i]<<"\t\t"<<Q2.nodes[2][i]<<endl;
}

//Q1.codes[] is an array of strings and it stores the huffman codes of the leaves


for(int i=Q2.maxSize-1; i>=0; i--){
    if(Q2.nodes[0][i]<0){
    Q2.code[-(Q2.nodes[0][i])-1]= Q2.code[i]+"0";
    }else{
        Q1.code[(Q2.nodes[0][i])-1]= Q2.code[i]+"0";
    }
    if(Q2.nodes[2][i]<0){
    Q2.code[-(Q2.nodes[2][i])-1]= Q2.code[i]+"1";
    }else{
        Q1.code[(Q2.nodes[2][i])-1]= Q2.code[i]+"1";
    }

}

cout<<"-----------------------------------------------------------"<<endl;
cout<<"Character"<<"\t"<<"Frequency"<<"\t"<<"Code"<<endl;
cout<<"-----------------------------------------------------------"<<endl;


for(int i=0; i<Q1.maxSize; i++){
    cout<<char(Q1.nodes[0][i])<< "\t\t"<<Q1.nodes[1][i]<<"\t\t"<<Q1.code[i]<<endl;
}

int sop = 0;
for(int i=0; i<Q1.maxSize; i++){
sop+=Q1.code[i].size() * Q1.nodes[1][i];
}
float acl= (float)sop/(float)str.size();
cout<<endl<<"Average code length = "<< acl<<endl<<endl;

//Encoding of the string to bitstring is done below

string bitString="";

for(int i=0; i<str.length();i++){
    for(int j=0; j<Q1.maxSize;j++){
        if(str[i]==char(Q1.nodes[0][j])){
            bitString+=Q1.code[j];
            j=Q1.maxSize;
        }
    }
}



float cr= (((float)str.size() * 8)- (float)bitString.size())/ ((float)str.size() * 8);

cout<<endl<<"Compression Ratio = "<<cr<<endl<<endl;

// decoding the bitString from the encoded string is done below.

string decodedStr="";

int i1=0;
while(i1<bitString.size()){

    int k1= -(Q2.maxSize);

    while(k1<0){
        if(bitString[i1]=='0'){
            k1=Q2.nodes[0][(k1*(-1))-1];
            i1++;
        }else{
            k1=Q2.nodes[2][(k1*(-1))-1];
            i1++;
        }
    }
    decodedStr+=char(Q1.nodes[0][k1-1]);
}



string revDecode="";

int i2=bitString.size()-1;
while(i2>=0){

    int k1= -(Q2.maxSize);

    while(k1<0){
        if(bitString[i2]=='0'){
            k1=Q2.nodes[0][(k1*(-1))-1];
            i2--;
        }else{
            k1=Q2.nodes[2][(k1*(-1))-1];
            i2--;
        }
    }
    revDecode+=char(Q1.nodes[0][k1-1]);
}
string compressedMsg = Q1.compressMessage(bitString);
cout<<endl;

cout<<"The original input text, The bitstring, The decoded string,"<<endl;
cout<<"the decoded string of reversed bitstring and the compressed message"<<endl;
cout<<"are written to a text file named output.txt."<<endl;

  ofstream myfile1;
  myfile1.open ("output.txt");
  myfile1 << "Original String: \n";
  myfile1<<str<<"\n\n\n";
  myfile1<<endl<<"Decoded string:"<<endl<<endl<<decodedStr<<endl;
  myfile1<<endl<<"The string after decoding reversed bitString: "<<endl<<endl<<revDecode<<endl;
  myfile1<<endl<<"The compressed string is: "<<endl<<endl<<compressedMsg<<endl;
  myfile1<<endl<<"Encoded bit string:"<<endl<<endl;
  for(int i=0;i<bitString.size();i++){
  myfile1<<bitString[i];
  if((i+1)%100==0)
  myfile1<<endl;
  }

  myfile1.close();

  return 0;
}

