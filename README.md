import math

import pygame
import random
pygame.init()

class DrawInformation:
    BLACK=0,0,0
    WHITE=255,255,255
    GREEN=15, 89, 6
    RED=255,0,0
    BACKGROUND_COLOR=WHITE

    FONT=pygame.font.SysFont('arial',20)
    SMALL_FONT=pygame.font.SysFont('arial',10)
    LARGE_FONT=pygame.font.SysFont('arial',60)

    GRADIENTS=[
        (128,128,128),(160,160,160),(192,192,192)
    ]

    SIDE_PAD=100
    TOP_PAD=150

    def _init_(self,width,height,lst):
        self.width=width
        self.height=height

        self.window=pygame.display.set_mode((width,height))
        pygame.display.set_caption('Sorting Algorithm Visualization')
        self.set_list(lst)
    def set_list(self,lst):
        self.lst=lst
        self.min_val=min(lst)
        self.max_val=max(lst)

        """Width of each block"""
        self.block_width=round((self.width-self.SIDE_PAD)/len(lst))
        """ """
        self.block_height=math.floor((self.height-self.TOP_PAD)/(self.max_val-self.min_val))
        self.start_x=self.SIDE_PAD//2

def draw(draw_info,algo_name,ascending):
    draw_info.window.fill(draw_info.BACKGROUND_COLOR)

    title=draw_info.LARGE_FONT.render(f"{algo_name} - {'Ascending' if ascending else 'Descending'}", 1, draw_info.GREEN )
    draw_info.window.blit(title, (draw_info.width / 2 - title.get_width() / 2, 5))

    controls=draw_info.FONT.render("R - Reset | Space - Start Sorting | A-Ascending | D-Descending",1,draw_info.BLACK)
    draw_info.window.blit(controls,(draw_info.width/2-controls.get_width()/2,75))

    sorting=draw_info.FONT.render("I - Insertion Sort", 1,draw_info.BLACK)
    draw_info.window.blit(sorting, (draw_info.width / 2 - sorting.get_width() / 2,100))

    draw_list(draw_info,{},ascending)
    pygame.display.update()

def draw_list(draw_info,color_positions={},ascending=True):
    lst=draw_info.lst
    # print(ascending)
    sorted = lst.copy()
    if ascending:
        sorted.sort()
    else:
        sorted.sort(reverse=True)
    pygame.draw.rect(draw_info.window,draw_info.BACKGROUND_COLOR,(draw_info.SIDE_PAD//2,draw_info.TOP_PAD,draw_info.width-draw_info.SIDE_PAD,draw_info.height-draw_info.TOP_PAD))
    for i,val in enumerate(lst):

        num_font=pygame.font.SysFont('arial',draw_info.block_width-5)
        num=num_font.render(f"{val}",1,draw_info.BLACK)
        x=draw_info.start_x+i*draw_info.block_width
        y=draw_info.height-(val-draw_info.min_val)*draw_info.block_height-8

        ## display num
        draw_info.window.blit(num,(x+((draw_info.block_width-num.get_width())/2),y-20))

        color=draw_info.GRADIENTS[i%3]
        if i in color_positions:
            color=color_positions[i]
        if sorted[i]==lst[i]:
            color=draw_info.GREEN
        pygame.draw.rect(draw_info.window,color,(x ,y,draw_info.block_width-1,draw_info.height))
    pygame.display.update()

def generate_starting_list(n,min_val,max_val):
    lst=[]
    for _ in range(n):
        val=random.randint(min_val,max_val)
        lst.append(val)
    return lst

def bubble_sort(draw_info,ascending=True):
    lst=draw_info.lst
    for i in range(len(lst)-1):
        for j in range(len(lst)-1-i):
            if lst[j]>lst[j+1] and ascending:
                lst[j],lst[j+1]=lst[j+1],lst[j]
                draw_list(draw_info,{j:draw_info.GREEN,j+1:draw_info.RED},ascending)
                # yield True
            if lst[j]<lst[j+1] and not ascending:
                lst[j],lst[j+1]=lst[j+1],lst[j]
                draw_list(draw_info,{j:draw_info.GREEN,j+1:draw_info.RED},ascending)
                # yield True
    return lst

def insertion_sort(draw_info,ascending=True):
    lst=draw_info.lst

    for i in range(1,len(lst)):
        temp=lst[i]
        j=i-1;
        while True:
            ascending_sort= j>=0 and lst[j]>temp and ascending
            descending_sort= j>=0 and lst[j]<temp and not ascending
            if not ascending_sort and not descending_sort:
                break;
            lst[j+1]=lst[j]
            lst[j]=temp
            j-=1
            draw_list(draw_info,{j+1:draw_info.GREEN,j: draw_info.RED},ascending)
            # yield True

    return lst

def QuickSort(draw_info,ascending=True):
     quickSort(draw_info,draw_info.lst,0,len(draw_info.lst)-1,ascending)
def quickSort(draw_info,lst,low,high,ascending):
    if low<high:
        mid=partition(draw_info,lst,low,high,ascending)
        quickSort(draw_info,lst,low,mid-1,ascending)

        quickSort(draw_info,lst,mid+1,high,ascending)
        # yield True

def partition(draw_info,lst,low,high,ascending):
    pivot_ind=low
    pivot=lst[low]

    while(low<high):
        if ascending:
            while low<len(lst) and lst[low]<=pivot:
                low+=1
            while lst[high] > pivot:
                high -= 1
        else:
            while low < len(lst) and lst[low] >= pivot:
                low += 1
            while lst[high] < pivot:
                high -= 1

        if low<high:
            lst[low],lst[high]=lst[high],lst[low]
            draw_list(draw_info,{low:draw_info.RED,high:draw_info.GREEN},ascending)
    lst[high],lst[pivot_ind]=lst[pivot_ind],lst[high]
    return high
def MergeSort(draw_info,ascending):
    mergeSort(draw_info,draw_info.lst,0,len(draw_info.lst)-1,ascending)
def mergeSort(draw_info,lst,l,r,ascending):
    if l<r:
        mid=(l+r)//2
        mergeSort(draw_info,lst,l,mid,ascending)
        mergeSort(draw_info,lst,mid+1,r,ascending)
        merge(draw_info,lst,l,mid,r,ascending)

def merge (draw_info,lst,l,m,r,ascending):
    L=lst[l:m+1]
    R=lst[m+1:r+1]
    i=j=0
    k=l
    while i<len(L) and j<len(R):
        if L[i]>R[j]:
            lst[k]=R[j]
            draw_list(draw_info, {j+m: draw_info.RED, k: draw_info.GREEN}, ascending)
            j+=1
        else:
            lst[k]=L[i]
            draw_list(draw_info, {i+l: draw_info.RED, k: draw_info.GREEN}, ascending)
            i+=1
        # draw_list(draw_info, {i + l: draw_info.RED, k: draw_info.GREEN}, ascending)
        k+=1
    while i < len(L):
        lst[k] = L[i]
        draw_list(draw_info, {i+l: draw_info.RED, k: draw_info.GREEN}, ascending)
        i += 1
        k += 1

    while j < len(R):
        lst[k] = R[j]
        draw_list(draw_info, {j+m: draw_info.RED, k: draw_info.GREEN}, ascending)
        j += 1
        k += 1
    
def main():
    run=True
    clock=pygame.time.Clock()

    n=50
    min_val=0
    max_val=100

    lst=generate_starting_list(n,min_val,max_val)
    draw_info=DrawInformation(1000,600,lst)

    sorting=False
    ascending=True

    sorting_algo=insertion_sort
    sorting_algo_name="Insertion Sort"
    # sorting_algo_generator=None
    while run:
        clock.tick(1)
        if sorting:
            # try:
            #     next(sorting_algo_generator)
            # except Exception as e:
            sorting=False 
        else :
            draw(draw_info,sorting_algo_name,ascending)
        pygame.display.update()
        for event in pygame.event.get():
            if event.type==pygame.QUIT:
                run=False
            if event.type !=pygame.KEYDOWN:
                continue
            if event.key ==pygame.K_r and not sorting:
                lst=generate_starting_list(n,min_val, max_val)
                draw_info.set_list(lst)
                sorting=False
            elif event.key==pygame.K_SPACE and not sorting:
                sorting=True
                sorting_algo(draw_info,ascending)
            elif event.key==pygame.K_a and not sorting:
                ascending=True
            elif event.key==pygame.K_d and not sorting:
                ascending=False
            elif event.key == pygame.K_i and not sorting:
                sorting_algo=insertion_sort
                sorting_algo_name="Insertion Sort"
            elif event.key == pygame.K_b and not sorting:
                sorting_algo = bubble_sort
                sorting_algo_name = "Bubble Sort"
            elif event.key== pygame.K_q and not sorting:
                sorting_algo = QuickSort
                sorting_algo_name = "Quick Sort"
            elif event.key == pygame.K_m and not sorting:
                sorting_algo = MergeSort
                sorting_algo_name = "Merge Sort"
            else:
                pass
    
    pygame.quit()

if __name__ == '_main_':
    main()
