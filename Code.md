```C++
//Written by __RaiyaN__

#include <bits/stdc++.h>
#define endl '\n'
using namespace std;
typedef long long ll;
#define forn(i,n) for(ll i=0;i < ll (n); i++)
#define scan(n) scanf("%lld", &n);
#define print(n) printf("%lld", n);
#define pb(n) push_back(n)

class SplayTree{
    struct Node{
        Node *Parent, *Left, *Right;
        ll Key, Sum;
    };
    Node* root = NULL;

    void FixSum(Node* N)
    {
        if(N == NULL) return;
        ll s = 0;
        if(N->Left != NULL) s+=N->Left->Sum;
        if(N->Right != NULL) s+=N->Right->Sum;
        N->Sum = s + N->Key;
        return;
    }

    void RightRotate(Node* P)
    {
        if(P==NULL || P->Left==NULL) return;
        Node* L = P->Left;
        Node* LR = L->Right;
        Node* PP = P->Parent;
        if(PP!=NULL)
        {
            if(PP->Right == P) PP->Right = L;
            else PP->Left = L;
        }
        if(LR!=NULL) LR->Parent = P;
        L->Parent = PP;
        L->Right = P;
        P->Parent = L;
        P->Left = LR;
        FixSum(LR);
        FixSum(P);
        FixSum(L);
        FixSum(PP);
        return;
    }

    void LeftRotate(Node* P)
    {
        if(P==NULL || P->Right==NULL) return;
        Node* R = P->Right;
        Node* RL = R->Left;
        Node* PP = P->Parent;
        if(PP!=NULL)
        {
            if(PP->Right == P) PP->Right = R;
            else PP->Left = R;
        }
        if(RL!=NULL) RL->Parent = P;
        R->Parent = PP;
        R->Left = P;
        P->Parent = R;
        P->Right = RL;
        FixSum(RL);
        FixSum(P);
        FixSum(R);
        FixSum(PP);
        return;
    }

    void Splay(Node* &Root, Node* N)
    {
        if(N==NULL) return;
        while(N->Parent!=NULL)
        {
            Node* P = N->Parent;
            Node* PP = P->Parent;
            if(PP==NULL)
            {
                if(P->Right == N) LeftRotate(P);
                else RightRotate(P);
                break;
            }
            if(PP->Left == P)
            {
                if(P->Left == N)
                {
                    RightRotate(PP);
                    RightRotate(P);
                }
                else
                {
                    LeftRotate(P);
                    RightRotate(PP);
                }
            }
            else
            {
                if(P->Right == N)
                {
                    LeftRotate(PP);
                    LeftRotate(P);
                }
                else
                {
                    RightRotate(P);
                    LeftRotate(PP);
                }
            }
        }
        Root = N;
        return;
    }

    Node* SplayFind(ll x, Node* &Root)
    {
        Node* v = Root;
        Node* next = NULL;
        Node* last = Root;
        while(v!=NULL)
        {
            if(v->Key >= x && (next==NULL || next->Key > v->Key)) next = v;
            last = v;
            if(v->Key == x) break;
            if(v->Key < x) v = v->Right;
            else v = v->Left;
        }
        Splay(Root, last);
        return next;
    }

    Node* Left_Descendant(Node *N)
    {
        if(N->Left == NULL) return N;
        else return Left_Descendant(N->Left);
    }

    Node* Right_Ancestor(Node *N)
    {
        if(N->Parent == NULL) return NULL;
        if(N->Parent->Left == N) return N->Parent;
        else return Right_Ancestor(N->Parent);
    }

    Node* Next(Node *N)
    {
        if(N->Right!=NULL) return Left_Descendant(N->Right);
        else return Right_Ancestor(N);
    }

    void Split(Node* Root, ll x, Node* &left, Node* &right)
    {
        right = SplayFind(x, Root);
        Splay(Root, right);
        if(right == NULL)
        {
            left = Root;
            return;
        }
        left = right->Left;
        if(left!=NULL) left->Parent = NULL;
        right->Left = NULL;
        FixSum(left);
        FixSum(right);
        return;
    }

    Node* Merge(Node* left, Node* right)
    {
        if(left == NULL) return right;
        if(right == NULL) return left;
        Node* Min_right = right;
        while(Min_right->Left != NULL)
        {
            Min_right = Min_right->Left;
        }
        Splay(right, Min_right);
        right->Left = left;
        left->Parent = right;
        FixSum(left);
        FixSum(right);
        root = right;
        return right;
    }

    void SplayInsert(ll x)
    {
        Node* left = NULL;
        Node* right = NULL;
        Node* N = NULL;
        Split(root, x, left, right);
        if(right==NULL || right->Key!=x)
        {
            N = new Node;
            N->Parent = NULL;
            N->Key = x;
            N->Sum = x;
            N->Left = NULL;
            N->Right = NULL;
        }
        root = Merge(Merge(left, N), right);
        return;
    }

    void SplayDelete(ll x)
    {
        Node* N = SplayFind(x, root);
        if(N!=NULL && N->Key==x)
        {
            Node* left = NULL;
            Node* right = NULL;
            Node* mid = NULL;
            Split(root, x, left, mid);
            Split(mid, x+1, mid, right);
            mid = NULL;
            root = Merge(left, right);
        }
        return;
    }

public:
    void Insert(ll x)
    {
        SplayInsert(x);
        return;
    }

    void Delete(ll x)
    {
        SplayDelete(x);
        return;
    }

    void Search(ll x)
    {
        Node* N = SplayFind(x, root);
        if(N==NULL || N->Key!=x) printf("Not found\n");
        else printf("Found\n");
        return;
    }

    ll Sum(ll l, ll r)
    {
        Node* left = NULL;
        Node* middle = NULL;
        Node* right = NULL;
        Split(root,l,left,middle);
        Split(middle,r+1,middle,right);
        ll s=0;
        if(middle!=NULL) s=middle->Sum;
        root = Merge(left, Merge(middle,right));
        printf("%lld\n", s);
        return s;
    }
};

int main()
{

    return 0;
}
```
